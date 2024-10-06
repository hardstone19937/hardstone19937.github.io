---
title: "操作系统学习"
weight: 4
---
# OS

操作系统学习心路历程。笔者学习了一下[**MIT6.1810**](https://pdos.csail.mit.edu/6.828/2023/schedule.html)。

## XV6源码研读——从启动开始

### 导引文件

在内核当中，有一个entry.S文件，目的是用汇编跳转运行start()函数。为了能够让entry.S放在最开始执行，这里有一个kernel.ld的连接脚本（loader?），通过连接脚本的编排，使得entry.S的位置在RAM开始的位置，也就是0x80000000。  

- 在汇编程序段当中有一些指令并不是真正的汇编指令，而是伪指令，这些指令会交给汇编器来处理，转换成真正的汇编指令。主要是为了方便程序段的书写。

在entry.S文件当中，有两个标签`_entry`和`spin`，其中`spin`标签是一个死循环。在`_entry`当中，使用了CSR寄存器之一的mhartid获取了运行当前代码段的硬件线程id。然后给这个对应的hart(硬件线程)开辟了对应的栈空间。接着调用`call`命令，跳转到`start()`函数所在的地址，此处的`call`应当是汇编伪指令，RISC-V下应该使用`jal/jalr`等指令。

- 使用过编译器的同学都知道，汇编是可以调用其他文件包括C文件的函数接口的。这里就是从entry.S跳转到start.c里面的start()函数的入口。

接下来我们来看start.c文件。这里涉及到一个汇编伪指令`mret`，这个指令的作用如下。

> An MRET or SRET instruction is used to return from a trap in M-mode or S-mode respectively.（risc-v特权指令手册）

`mret`指令的作用是从M特权陷阱当中返回。具体来讲，这个伪指令的具体动作包括：
1. 特权模式修改为xPP位段所装载的特权模式。
2. xIE位段的值修改为xPIE。
3. xIE位段的值设置位1。
4. xPP位段设置位最低支持的特权模式，如果xPP不是M，xRET会设置MPRV=0。

从这一系列动作可以看出。M的特权>S的特权>U的特权。调用RET会降低特权模式，因为xPP段会被硬件清零。这体现了硬件上的权限管理。

回到`start()`函数中，我们可以看到首先设置了`m_status`寄存器当中的xPP位段，把其中特权模式设置为S。然后，它往`mepc`（Machine exception program counter register.）寄存器里写入内核的`main`函数的地址。其余动作。

1. 关闭虚拟地址寻址。`satp`寄存器设置寻址模式，设置为0，其mod字段也为0，表示直接的物理地址寻址。

2. 设置`medeleg`和`mideleg`寄存器，授予S模式全部的陷阱和异常异常。

3. 设置`sie`寄存器，使能S模式外设，时钟，软件中断。

4. 设置`pmpaddr0`和`pmpcfg`寄存器，使得S模式能够访问所有的地址空间

5. 如下

调用了`timerinit`函数。在`timerinit`函数当中设置一些关于时钟中断的寄存器。

首先计算当前硬件线程的CLINT中断地址，然后将配置时钟的数据，写到某个数据区域当中，再把数据区域的指针记录到特权模式的临时寄存器`mscratch`当中，方便后续再`timervec`程序段当中使用。

设置`mtvec`寄存器，配置陷阱中断基地址。指向`timervec`程序段。在这个程序段当中，使用`mscratch`寄存器当中的数据，根据设置的时间间隔，设置下一个时钟中断的时机，安排S模式的软件中断，在时钟中断服务函数之后，这里使用了`sip`寄存器( Supervisor interrupt-pending register)。返回。

然后使能当前硬件线程全局中断，使能机器中断。

6. 最后把硬件线程id保存在这个cpu的tp寄存器当中。

接下来就是最上面提到的`mret`了，从当前特权陷阱中返回，硬件自动切换特权模式从M到S，而且触发硬件自动恢复现场的特性，把先前写在`mepc`寄存器中`main`函数的地址，赋值给`pc`寄存器。

至此，程序以特权模式S，或者说内核态，运行`kernel`。

## 页表与虚拟地址

MIT6.1810的Lab3是关于页表和虚拟地址的。有一个困惑，比如这样一个简单的C语言程序，它是如何从虚拟地址0x0001处找到数的呢？
```c
int main() {
    int p = *((int *)0x0001);
    return 0;
}
```

课程手册book-riscv-rev3中说明
> As a reminder, RISC-V instructions (both user and kernel) manipulate virtual addresses.  

RISC-V的指令不管是用户态还是内核态操作的均是虚拟地址。使用objdump工具分析一下程序的汇编看看。下面是x86的汇编，没有交叉编译用RISC-V。  

```asm
0000000000001129 <main>:
    1129:       f3 0f 1e fa             endbr64
    112d:       55                      push   %rbp
    112e:       48 89 e5                mov    %rsp,%rbp
    1131:       b8 01 00 00 00          mov    $0x1,%eax
    1136:       8b 00                   mov    (%rax),%eax
    1138:       89 45 fc                mov    %eax,-0x4(%rbp)
    113b:       b8 00 00 00 00          mov    $0x0,%eax
    1140:       5d                      pop    %rbp
    1141:       c3                      retq
    1142:       66 2e 0f 1f 84 00 00    nopw   %cs:0x0(%rax,%rax,1)
    1149:       00 00 00
    114c:       0f 1f 40 00             nopl   0x0(%rax)
```

把取数的那一行注释掉之后。
```asm
0000000000001129 <main>:
    1129:       f3 0f 1e fa             endbr64
    112d:       55                      push   %rbp
    112e:       48 89 e5                mov    %rsp,%rbp
    1131:       b8 00 00 00 00          mov    $0x0,%eax
    1136:       5d                      pop    %rbp
    1137:       c3                      retq
    1138:       0f 1f 84 00 00 00 00    nopl   0x0(%rax,%rax,1)
    113f:       00
```
可以观察发现多了三行
```asm
    1131:       b8 01 00 00 00          mov    $0x1,%eax
    1136:       8b 00                   mov    (%rax),%eax
    1138:       89 45 fc                mov    %eax,-0x4(%rbp)
```
查阅资料或者问大模型可以知道这段汇编代码的含义，不赘述，rax，eax和ax是同一64位寄存器的不同部分。在Linux/Unix环境下，会把源码编译成ELF(The Executable and Linking Format)文件。使用readelf工具可以读取ELF文件的信息。
```
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              DYN (Shared object file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x1040
  Start of program headers:          64 (bytes into file)
  Start of section headers:          14608 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         13
  Size of section headers:           64 (bytes)
  Number of section headers:         29
  Section header string table index: 28
```
上面是ELF文件的头文件结构。ELF里面并没有包含关于页表的信息，其中主要包含了程序和数据等，程序中所有涉及的地址均是虚拟地址。进一步研究，操作系统运行一个程序的过程。  

以下是个人理解。  
存储分为内存和外存，内存一般是那种内存条，外存一般是硬盘，而上述的汇编指令的虚拟地址页都是在内存中某个位置。程序编译完成后以二进制的形式存储在硬盘内。硬盘的读写需要用文件系统管理，比较早的文件系统有FAT32，暂且不谈。当我们要运行某个程序了，操作系统会把把程序加载进内存。这里用到一个关键的寄存器satp，xv6手册中如是写道。

> To tell a CPU to use a page table, the kernel must write the physical address of the root pagetable page into the **satp** register.   

在riscv-privileged手册当中有描述satp寄存器。 
> **Supervisor Address Translation and Protection (satp) Register**  
> This register holds the physical page number (PPN) of the root page table

硬件下固化了xv6手册当中所描述的三级页表，具体可能也可以通过某些寄存器进行其他配置，satp寄存器指三级页表的根页表的物理页。在任何用户进程启动的时候，或者操作系统开始运行的时候的系统进程启动时候，都会写一个物理页地址到satp寄存器当中，这样在这个进程运行的时候，所有的虚拟地址均会被硬件翻译成对应的物理页地址。操作系统启动的时候物理页可能是从磁盘中直接加载的，如果是新启动的用户态的进程，则要操作系统分配空闲的物理页，构造成三级页表，再用内嵌汇编把根物理叶地址写到satp寄存器当中。这样我们写的用户程序就能使用虚拟地址了。

## xv6的启动流程

### 导引文件

在内核当中，有一个entry.S文件，目的是用汇编跳转运行start()函数。为了能够让entry.S放在最开始执行，这里有一个kernel.ld的连接脚本（loader?），通过连接脚本的编排，使得entry.S的位置在RAM开始的位置，也就是0x80000000。  

- 在汇编程序段当中有一些指令并不是真正的汇编指令，而是伪指令，这些指令会交给汇编器来处理，转换成真正的汇编指令。主要是为了方便程序段的书写。

在entry.S文件当中，有两个标签`_entry`和`spin`，其中`spin`标签是一个死循环。在`_entry`当中，使用了CSR寄存器之一的mhartid获取了运行当前代码段的硬件线程id。然后给这个对应的hart(硬件线程)开辟了对应的栈空间。接着调用`call`命令，跳转到`start()`函数所在的地址，此处的`call`应当是汇编伪指令，RISC-V下应该使用`jal/jalr`等指令。

- 使用过编译器的同学都知道，汇编是可以调用其他文件包括C文件的函数接口的。这里就是从entry.S跳转到start.c里面的start()函数的入口。

接下来我们来看start.c文件。这里涉及到一个汇编伪指令`mret`，这个指令的作用如下。

> An MRET or SRET instruction is used to return from a trap in M-mode or S-mode respectively.（risc-v特权指令手册）

`mret`指令的作用是从M特权陷阱当中返回。具体来讲，这个伪指令的具体动作包括：
1. 特权模式修改为xPP位段所装载的特权模式。
2. xIE位段的值修改为xPIE。
3. xIE位段的值设置位1。
4. xPP位段设置位最低支持的特权模式，如果xPP不是M，xRET会设置MPRV=0。

从这一系列动作可以看出。M的特权>S的特权>U的特权。调用RET会降低特权模式，因为xPP段会被硬件清零。这体现了硬件上的权限管理。

回到`start()`函数中，我们可以看到首先设置了`m_status`寄存器当中的xPP位段，把其中特权模式设置为S。然后，它往`mepc`（Machine exception program counter register.）寄存器里写入内核的`main`函数的地址。其余动作。

1. 关闭虚拟地址寻址。`satp`寄存器设置寻址模式，设置为0，其mod字段也为0，表示直接的物理地址寻址。

2. 设置`medeleg`和`mideleg`寄存器，授予S模式全部的陷阱和异常异常。

3. 设置`sie`寄存器，使能S模式外设，时钟，软件中断。

4. 设置`pmpaddr0`和`pmpcfg`寄存器，使得S模式能够访问所有的地址空间

5. 如下

调用了`timerinit`函数。在`timerinit`函数当中设置一些关于时钟中断的寄存器。

首先计算当前硬件线程的CLINT中断地址，然后将配置时钟的数据，写到某个数据区域当中，再把数据区域的指针记录到特权模式的临时寄存器`mscratch`当中，方便后续再`timervec`程序段当中使用。

设置`mtvec`寄存器，配置陷阱中断基地址。指向`timervec`程序段。在这个程序段当中，使用`mscratch`寄存器当中的数据，根据设置的时间间隔，设置下一个时钟中断的时机，安排S模式的软件中断，在时钟中断服务函数之后，这里使用了`sip`寄存器( Supervisor interrupt-pending register)。返回。

然后使能当前硬件线程全局中断，使能机器中断。

6. 最后把硬件线程id保存在这个cpu的tp寄存器当中。

接下来就是最上面提到的`mret`了，从当前特权陷阱中返回，硬件自动切换特权模式从M到S，而且触发硬件自动恢复现场的特性，把先前写在`mepc`寄存器中`main`函数的地址，赋值给`pc`寄存器。

至此，程序以特权模式S，或者说内核态，运行`kernel`。