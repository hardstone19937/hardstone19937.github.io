---
title: "Batch脚本考古"
---
# Batch
`云云：batch脚本已经死了`  
怎么说呢，batch脚本确实比较难用，教程残缺不全，毕竟是这是上古时代的遗物，和DOS系统有着非常密切的关系。众所周知，windows有着其他系统不具备的强大兼容性，同时这也让windows系统背上了沉重的历史包袱。（windows是不是读该成win DOS\[doge\]）  
因此，batch有着DOS时代的眼泪（悲），不过我们就像一个优雅的爵士一样，我们可以把他装裱起来放在柜台上，时时把玩欣赏，同时我们可以感受到最质朴的脚本语言之一的样子。  
给想学习bat脚本的人推荐一个网站：  
[bat脚本之家](http://www.bathome.net/index.php)  
（链接以php结尾，php也可能是时代的眼泪吧）  
一点点的罗列知识点是过于乏味的，我想通过一个个的尝试来加深对这个事物的认知。所有看到本文的人都能够一起动手尝试。  
环境和工具：Windows操作系统。  

**官方资料: [CMD文档](https://learn.microsoft.com/zh-cn/windows-server/administration/windows-commands/windows-commands)**  

## Try1 Hello, World!

***梦开始的地方***

首先，创建一个txt文件(最好用英文路径，不过windows中文支持挺好的?)，在里面复制如下代码。  
```bat
@echo off
echo Hello, world!
pause
```

然后，将txt的后缀改成bat，鼠标双击运行可以看到
```txt
Hello, world!
请按任意键继续. . .
```

查阅资料思考问题：

- `@echo off`删去后会怎样？
- windows和Linux的`echo`有什么区别？
- `pause`有什么作用？

## Try2 一个编译运行脚本

在windows系统中，有很多的IDE(Integrated Development Environment，集成开发环境)。比如我们在编写C++程序的时候，集成开发环境比如说Jetbrains家的Clion，微软的Visual Studio，或者简陋一点的Dev C++，它们都提供了一键编译的GUI。另外，虽然Vscode并不是IDE，不过内置的插件可以帮你完成这些步骤。但是如果我们回归到最朴素的样子，这里有一个编辑工具(比方说，gVim，Vscode，甚至是记事本，这里笔者用的Vscode)，并且有某种语言的编译器，或者说解释器，我们要在这个基础上编写一个功能脚本，实现一系列的操作帮我们减少重复劳动，提高工作效率。  

查阅资料思考问题：

- “环境变量”是什么意思，它的本质是什么？
- 如何添加环境变量？（Linux添加环境变量和Windows添加环境变量）
- 当前工作目录是什么？
- C程序和Python中的相对路径是以什么为基准？

假设文件目录是这样的

```
./
../
run.bat
C++/
    a.exe
    in.txt
    ok.cpp
    out.txt
    std_out.txt
```
> 笔者喜欢打[codeforces](https://codeforces.com/)所以编写了下面的脚本。  
> 下面是一段脚本，它首先切换到源代码所在目录C++/编译运行filename中设置的源代码文件，编译运行得到从in.txt中读入数据，运行输出到out.txt，并且和答案的std_out.txt进行比较，如果编译失败或者是答案错误会输出FAIL，否则输出OK。这个脚本还支持两个选项，-i忽略和答案的比较，-n取消编译，只运行。
> 运行这个脚本需要将编译设置到环境变量，Windows一般是MinGW。

`说教是无力的，自主查阅资料的过程收获颇丰`

一些需要的知识:

- bat脚本中，cd，set等命令的含义和用法。
- 如何获取传给脚本的参数？
- 如何获取上一条命令的返回值？
- 标准输入输出重定向为文件
- bat脚本字符的处理。


```bat
@echo off
cd C++

set filename=ok.cpp
set date=%DATE:~0,10%
set time=%TIME:~0,8%
echo System time %date% at %time% 

for /f "tokens=1-3 delims=/- " %%i in ("%date%")do set/a y=%%i,m=%%j,d=%%k
set date=%y%/%m%/%d%

if "%1" neq "-n" (
    echo ***start to complile***
    
    g++ -Wall %filename% -o a.exe -O2
    
    for /f "tokens=1,2 delims= " %%a in ('forfiles /m a.exe /c "cmd /c echo @fdate @ftime"') do (
        if "%%a" neq "%date%" (
            echo ***fail to complie***
            goto ERROR
        )
        if "%%b" lss "%time%" (
            echo ***fail to complie***
            goto ERROR
        )
    )
    echo ***complied successfully***
)
echo ***begin to run***
a <in.txt> out.txt

echo ***finsih running***

if "%1" == "-i" (
    goto YES
)

fc /w out.txt std_out.txt

if %Errorlevel% == 0 (
:YES
    echo   ___  _   __
    echo  / _ \^| ^| / /
    echo ^| ^| ^| ^| ^|/ / 
    echo ^| ^| ^| ^|   ^<  
    echo ^| ^|_^| ^| ^|\ \ 
    echo  \___/^|_^| \_\
if "%1" == "-i" (
    goto EOF
)
) else (
:ERROR
echo  ______      _____ _      
echo ^|  ____/\   ^|_   _^| ^|     
echo ^| ^|__ /  \    ^| ^| ^| ^|     
echo ^|  __/ /\ \   ^| ^| ^| ^|     
echo ^| ^| / ____ \ _^| ^|_^| ^|____ 
echo ^|_^|/_/    \_\_____^|______^|
                           
                          
)
:EOF

```
运行的效果类似
```
System time 2024/06/03 at 21:24:45
***start to complile***
***complied successfully***
***begin to run***
***finsih running***
正在比较文件 out.txt 和 STD_OUT.TXT
FC: 找不到差异

  ___  _   __
 / _ \| | / /
| | | | |/ / 
| | | |   <  
| |_| | |\ \ 
 \___/|_| \_\
```


回到正文，笔者在尝试的时候遇到很多问题，看似简单的脚本。读者可以自己去尝试的写写类似的脚本试试。在使用g++编译的时候，笔者发现，就算是编译失败后，终端获取的命令的返回值仍然是0，所以只能比较当前终端开始的时间和文件上次更新的时间，因为当g++编译失败的时候不会去更新已经编译的exe文件。windows系统本身的以GUI为主，所以脚本命令相对Linux而言功能弱一些，但是提供的API也是很多的，功能也足够强大。注意了，一个零碎的小知识点，类似Linux的man命令可以查看各个选项(在windows里面不叫“选项”叫“开关”)，比如，我们查看set的选项可以在cmd窗口内键入`set /?`，即可查看相关选项的含义了，这个应该是DOS系统留下来的传统，之前笔者在摆弄DOSBOX的DEBUG工具的时候也是类似的。读者可能觉得这样编译不麻烦吗？其实也不麻烦，把Vscode设置成文件自动保存（不然编译的是旧文件）后，按``Ctrl + ` ``就能打开终端，一般是powershell，键入./run.bat，如果有历史记录的话可按方向上键，回车即可运行。  

那个OK字符是我用网站生成的，有需要的可以用  
[字符画生成网站](https://patorjk.com/software/taag/#p=display&f=Graffiti&t=Type%20Something%20)

推荐查阅：

- forfiles的使用方法
- for的使用方法

留作读者思考:

- 使用Windows原生的Command Prompt(CMD)和Powershell运行bat脚本有什么区别？
- 为什么笔者没有选择打印彩色字，而是使用字符画打印。
- echo里面的`^`符号的作用是什么呢？

---
2024/6/8日修正：上述代码存在一定的问题。(っ °Д °;)っ  
读者发现了吗？

`set \a d=%%k`和`set \a d=%%j`均可能出现问题。  
研究一下`set \a`使用命令`set \?`我们可以看到下面这段话。

```
/A 命令行开关指定等号右边的字符串为被评估的数字表达式。该表达式
评估器很简单并以递减的优先权顺序支持下列操作:

    ()                  - 分组
    ! ~ -               - 一元运算符
    * / %               - 算数运算符
    + -                 - 算数运算符
    << >>               - 逻辑移位
    &                   - 按位“与”
    ^                   - 按位“异”
    |                   - 按位“或”
    = *= /= %= += -=    - 赋值
      &= ^= |= <<= >>=
    ,                   - 表达式分隔符

如果你使用任何逻辑或取余操作符， 你需要将表达式字符串用
引号扩起来。在表达式中的任何非数字字符串键作为环境变量
名称，这些环境变量名称的值已在使用前转换成数字。如果指定
了一个环境变量名称，但未在当前环境中定义，那么值将被定为
零。这使你可以使用环境变量值做计算而不用键入那些 % 符号
来得到它们的值。如果 SET /A 在命令脚本外的命令行执行的，
那么它显示该表达式的最后值。该分配的操作符在分配的操作符
左边需要一个环境变量名称。除十六进制有 0x 前缀，八进制
有 0 前缀的，数字值为十进位数字。因此，0x12 与 18 和 022
相同。请注意八进制公式可能很容易搞混: 08 和 09 是无效的数字，
因为 8 和 9 不是有效的八进制位数。
```

注意到08和09是非法的赋值，所以上述代码再每月的8号，9号或者每年的8月，9月会出现bug，出现如下报错。  
```
无效数字。数字常数只能是十进制(17)，十六位进制(0x11)或
八进制(021)。
```

[解决方案](https://blog.csdn.net/chuangxin/article/details/104387964)

---
2024/6/12补充： 
将这个脚本移动到同学的电脑上发现问题，他电脑上没办法和我一样运行这个脚本。问题的原因是`%DATE%`获取的时间格式不同。据说，他调整了windows系统时间。也可能`%DATE%`获取系统时间的兼容性有问题。
> 生活不只有用之用，无用之用亦为美