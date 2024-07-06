# 前言

操作系统学习心历程。笔者学习了一下[**MIT6.1810**](https://pdos.csail.mit.edu/6.828/2023/schedule.html)。 

# 心路历程

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