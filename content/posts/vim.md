--- 
title: "Vim的使用"
date: 2024-08-07T11:30:03+08:00
---

# 前言

1. 会尽量避免图片的使用，方便代码仓库的更新。 
2. 一些名词会进行解释，在编号较小的文章里面解释过的会减少解释，或者不解释。  
3. 如果有纰漏还请指正，笔者还是个newbie。

# 梦的开始

本章可以随便看看。

> 在《太空漫游2001》中，古猿将骨头抛向空中欢呼的瞬间，骨头变成了遨游太空的空间站。古猿用骨头来制作工具，捕获猎物，人类使用空间站去探索外界，在宇宙的终极智慧面前，人类和古猿都沉浸在使用自己微薄智慧所创造的工具的喜悦中，又何尝有不同呢？  

> 计算机学习很多时候是在学习使用新的工具，工具种类繁多，有成套成套的工具链，深入理解所有工具的底层不太现实，大多都是边学边用，像把玩一个黑盒子一样，在使用的过程中逐渐理解本质。国外的教科书开篇一般喜欢讲一些历史之类的放松一下心情，这里也先来了解一下什么是Vim。正所谓格物致知。笔者以前听电台讲格物致知，讲的是王阳明格竹子，就是干瞪眼看着竹子看了七天，然后大病一场，不仅知识没有学到，还把身体搞坏了。不过现在格物致知的意思已经不是那种“格”，而是了解事物本身，然后得到知识。

首先，Vim是编辑器，和Vscode，Sublime甚至是Windows记事本是同一类型的（~~替身使者~~），当然Vim具备的功能比记事本多多了，有很多方便程序员开发的功能。IDE(Integrated Development Environment，集成开发环境)，比上面这种强化版本的记事本更进一步，集成了各种调试工具，运行环境，比如Microsoft的Visual Stduio，JetBrains的IDEA，Xilinx的Vivado，还有最近鸿蒙开发的DevEco。集成开发环境主打一个即开即用，像一个精装房，直接就能拎包入住。Vim之类的编辑器主要就是实现了编辑功能，其他功能，比如Debugger(野路子编程，笔者用得也比较少，printf大法爱好者)没有，类似一个毛坯房，需要自己折腾。其实功能强到一定程度也能叫做IDE了，Vscode通过安装插件可以变得非常强大，可以称为一个轻量级的IDE，不过Vscode面对大型项目的时候可能会性能不足，有些IDE也需要简单配置一番，并不能直接使用。Vscode既可以说是功能强大的编辑器，也可以说是轻量级的IDE。事实上，2024年了，IDE和编辑器的界限也没有那么突出。


回到Vim，这里指原汁原味的Vim，不添加任何的插件，也不编写复杂的vimscript脚本(Vim的配置脚本语言，笔者不会，除了简单的set)，本文也主要讲原味Vim的使用。原版的美学就像玩Minecraft原版单机生存一样。原味Vim的功能已经有很多了，原生支持多种语言高亮。不过也会拓展一些其他玩法。

一些建议:  
1. 现在已经是2024年了，大模型有这么多为何不去问问大模型呢？大模型汇集天地之精华，问问没准就能得到答案(不过现在(2024.8.7)个人感觉还是问ChatGPT比较有用，毕竟人家英语语料练得多，计算机领域的项目的文档很多都是英文)。
2. 可以去互联网上碰碰运气，虽然Vim现在是比较小众，不过Vim历史比较悠久，各位远古大神的文章可以博观而约取之。想办法把问题表述得更加清楚，找到合适的搜索关键词。

回到Vim，Vim是一个免费的开源的文本编辑器，改进自Bill Joy的Vi。Vim的作者是Bram Moolenaar（他的事迹可以去了解一下，2023年去世了，Vim打开会有“帮帮乌干达的可怜儿童”也是因为他）。Vim第一次发布于1991年，现在已经33年了。Vim原本是配备在Linux系统上的，开源软件一般在开源系统上会好用一些，在闭源系统上更新会慢一些，Windows上也有一款gVim可以使用，用起来差点意思而且相关的一些资料也会少很多，不过基本操作差不多，一般都是在Linux上用Vim，不过也有人在Windows上用gVim编写Verilog。Vim是Vi improved(改进的Vi)的缩写。Vi是Visual的缩写。

# 从说明书开始

一个好的开源项目需要有一个好的文档，其实任何软件都应是如此，说明文档需要把软件的功能讲清楚。不过手机app之类的从来没有啥教程，可能都是基于常识，所见即所得吧。实际上，Linux环境下基本上每个软件都有文档。使用`man`或者`help`就能查看软件的使用说明了，在《鸟哥的Linux私房菜》里面经常强调。

# Take a bite!

Linux下我都用下面的形式，读者可以使用`man`或`help`试试。`man`的全称应是`manual`，使用手册。
```shell
$ man vim
```
或
```shell
$ vim --help
```
读者可以浏览一下，help得到的说明比较简短，man得到的说明比较长。文档这么长，详细阅读实在是为难人。
比如gcc的文档
```shell
$ man gcc
```
按`G`跳到末尾(注意是大写的`G`)，发现有1.8w多行(gcc version 9.4.0 Ubuntu 9.4.0-1ubuntu1~20.04.2)。其实`man`也有很多指令，查看文档经常都是搜索关键词。在输入`man vim`之后，可以再输入`/xxx`在文档中查询字符串`xxx`，再输入`n`可以查找下一处，或者输入`shift+n`查找上一处。`shift+n`其实就是`N`，输入单个大写字符的时候可以使用`shift`。简单用Python写一个HelloWorld。
```shell
$ vim hello.py
```

在Linux的终端窗口或者通过SSH连接到Linux服务器上的终端上键入上述命令，之后会形成一个名字叫.hello.py.swp的文件.swp是swap的意思，交换文件。在Windows当中，使用word编辑文件的时候也会生成一个临时文件，比如在某个文件夹里创建一个test.docx，然后右键打开Command Prompt终端，再用Word打开test.docx，在CMD终端里面键入`dir /a`可以看到
```cmd
2024/08/15  14:20    <DIR>          .
2024/08/15  14:20    <DIR>          ..
2024/08/15  14:20                 0 test.docx
2024/08/15  14:20               162 ~$test.docx
               2 个文件            162 字节
               2 个目录 82,243,371,008 可用字节
```
有个~$test.docx的不可见文件。同样的vim也有一个这样的交换文件，里面维护着你对这个文档的操作，当你进行保存的时候，才会把这些操作更新到文件当中。如果没有保存就会丢失这些更新。回到Vim，如果此时你再打开一个终端窗口试图再向hello.py写入

再次敲入
```shell
$ vim hello.py
```
会得到如下的情况
```txt
E325: ATTENTION
Found a swap file by the name ".hello.py.swp"
          owned by: hardstone   dated: Thu Aug 15 14:08:53 2024
         file name: ~hardstone/hello.py
          modified: no
         user name: hardstone   host name: blackstone
        process ID: 1643 (STILL RUNNING)
While opening file "hello.py"
      CANNOT BE FOUND
(1) Another program may be editing the same file.  If this is the case,
    be careful not to end up with two different instances of the same
    file when making changes.  Quit, or continue with caution.
(2) An edit session for this file crashed.
    If this is the case, use ":recover" or "vim -r hello.py"
    to recover the changes (see ":help recovery").
    If you did this already, delete the swap file ".hello.py.swp"
    to avoid this message.

Swap file ".hello.py.swp" already exists!
[O]pen Read-Only, (E)dit anyway, (R)ecover, (Q)uit, (A)bort:
```
因为.hello.py.swp已经存在所以不能直接编辑hello.py，下面也提供了若干选项。回到原先编辑hello.py的窗口。Vim存在多种模式，刚进入Vim是**普通模式(normal mode)**，此时按`i`键，注意大小写敏感，可以进入编辑模式。按了`i`键之后，下面状态栏可以看到。
```txt
-- INSERT --                                             1,1           All 
```
说明此时是INSERT(插入)模式，`1,1`表示当前光标的位置。

简单写一句
```python
print("hello, world!")
```
保存文件需要退出编辑模式，按`Esc`键，再按`:`，此时可以输入各种各样的命令，保存文件可以输入`w`，然后回车，当然，不嫌麻烦的话可以输入`write`，再同样的按(此时仍然是命令模式)`:`，键入`q`，当然也可以是`quit`，就能退出了。如果修改了文件里的内容，并且没有`w`写入保存，直接`q`会出现错误`E37: No write since last change (add ! to override) `。在Vim当中有很多这样的缩写，在编写Vimrc文件的时候常常可以遇到。上面的两个命令可以写到一起，直接`Esc`键，键入`:wq`，写入保存退出。

运行一下。
```shell
hardstone@blackstone:~$ python3 hello.py
hello, world!
```
hello, world! 进入Vim，键入`:h`或者`:help`能够进入自带的文档，这个文档是相当方便的，内置标签文件(tags)能够支持跳转，强烈建议浏览一下。看完整个文档就彻底学会当然是不现实的，重要的是学会去查去用这些文档，不管什么东西拿过来，通过查阅资料迅速掌握上手也是能力的一部分。

# Learn to Read!

在普通模式(不是普通模式的按`Esc`)，输入`:h`或者`:help`，回车进入help.txt页面。抬头一行。
```txt
help.txt For Vim version 9.0. Last change: 2022 May 13
```

首先是Move around词条告诉我们如何上下作用移动光标，右边画了一个 h j k l的方位图。其实不习惯h j k l也没关系，直接用方向键就行了，怎么舒服怎么来，据说是以前的键盘没有方向键。

看到"With the mouse"这个词条。以前甚至是没有鼠标的，鼠标这个特性后来版本的Vim才加上去的。注意：需要在远程连接终端或者装了GUI(Graphical User Interface，图形用户接口)的Linux本机上。如果在没有GUI的Linux上，就算配置了使能鼠标也是没用的。

## 配置文件

Vscode使用json配置。同样地Vim也有配置文件，但是使用vimscript语言。不知道应该说古早时代生态壁垒比较严重，还是对程序员的要求比较高，像Make软件需要编写Makefile，Shell需要编写shell脚本，Emacs(和Vim同时代的编辑器)使用的是Emacs Lisp语言配置，还有其他等等，一个软件一个语言。以Ubuntu20.04为例，Vim的配置文件存放在两个位置，一个是作用于全局的vimrc，rc是run commands的意思，启动vim的时候自动运行的命令。
```txt
/etc/vim/vimrc
```
另一个文件位于当前用户目录，也就是`~`所表示的目录，即`home/用户名`，在`home`目录下有多个用户名，每个用户名都有一个文件夹，输入`cd`命令可以切换到当前用户根目录位置。同样地，在Windows上也有，C盘有个`用户`文件夹，里面是注册在这台电脑上的用户。

加载顺序往往是“从大到小”，全局的配置(`/etc/vim/vimrc`)先读入，再读入用户级别配置覆盖(`~/.vim/vimrc`)。

> 小插曲：前几天(2024/9/1)，使用node js的时候也发现类似的，其实很多软件的配置逻辑也都是如此。node js在安装目录下有全局的.npmrc文件，在用户目录下也有用户级别的.npmrc文件，在项目当中也有也有一个.npmrc。因为镜像源坏了，要换一个，但是发现在工作目录下怎么也改不了，找了半天才发现工作目录有个.npmrc文件，直接覆盖掉了。成熟的shell都有完整的补全功能，多按按Tab键补全，减轻负担。
```shell
$ cd #切换到用户根目录
$ mkdir ./vim #(可选)如果没有.vim文件夹的话
$ vim ./vim/vimrc #编辑当前用户vim的配置
```
按i进入，insert模式，写入一些配置。
```vimscript
set mouse=a
set nu
set tabstop=4
set softtabstop=4
set shiftwidth=4
set expandtab
```
上面的代码是什么意思，可以试试去问问大语言模型(LLM，Large Language Model)。主要是启用鼠标，行号，以及制表键用4个空格代替。

## vimtutor

回到说明书当中，里面的Getting Started部分。良好的文档中一般有示例，特别是quick start, get started之类的，引导新手做一些简单操作或者简单运行一个Demo。在Linux上完整安装的Vim，会附带安装一个小的练习程序vimtutor，Windows上的gVim貌似不行功能不完整。
直接在终端键入
```shell
$ vimtutor
```
**强烈建议**仔细过一遍。vimtutor其实就是用vim打开一个文本文件，里面有一些操作案例。里面将了基本的操作，有些操作特别的实用，有很多vim的常识。
# 一些比较实用的操作

**再次建议，先仔细过一遍`vimtutor`里面的教程!!!**

## 移动

移动是编辑器的基本操作，除了常规的h j k l或者上下左右移动，按单词移动也是非常实用的操作，比如在一些项目中，单词(或者从编程语言的角度，token)之间的空格留很多。特别是在嵌入式开发的纯C项目当中，封装宏的时候往往如此。还有Verilog之类的。一点点移动就显得太低效了。按单词移动，能从一个单词移动到相邻的单词。颇为惊艳。

- `w`：移动到下一个单词的开头（forward word）
- `e`：移动到当前单词的末尾，然后移动到下一个单词尾部（end of word）
- `b`：移动到当前单词的开头，然后移动到前一个单词的开头（back word）

行级别的移动。

- `$`: 移动到行尾(有的正则表达式用\$作为结尾)。
- `^`: 移动到行首。也可以按数字0(有的正则表达式用^作为开头)。
- `G`: 移动到最后一行(Goto)。
- `gg`: 移动到第一行(go go!)。
- `:[number]<Enter>`: 比如`:10`回车，跳转到第10行。也可以使用`[number]G`

比较有趣的移动
- `%`: 如果光标刚好指向某个括号，会移动到相配对的另一个括号的位置，如果不在括号上，会在本行内向后找一个括号。

## 编辑

- `i`: 在光标当前位置插入(insert)。
- `a`: 在光标当前字符后一个字符插入，大A是在行末插入(append)。
- `o`: 在光标所在行的下一行另起的行，并进入编辑模式，大O是在上一行另起一行(open a new line)。
- `R`: 注意大写。类似Windows的Insert模式，打开word按ins键(Replace)。

## 删除、复制、粘贴与组合命令

常见的比如删除一行，复制一行。

- `yy`: 复制当前行(yank)。
- `dd`: 删除，实际上是剪切一行(delete)。
- `p` : 粘贴到当前行下方(paste)。

比如想要复制多行。一种方法是可以使用数字组合`yy`，比如`4yy`可以复制4行，另一种方法是使用`v`命令。进入visual模式，移动光标位置可以获得一个选区。可以使用`y`复制，使用`d`删除，使用`p`直接覆盖粘贴。

比较实用的多行同时编辑。
- 多行注释: 按`ctrl+v`进入块visual模式，移动位置可以构造一个长方形选区。再按`shift+i`也就是大写的I，可以进入编辑模式，此时编辑的是长方形选区的第一行，我们可以添加`//`，再按两次`<Esc>`键就能在多行同时添加`//`。

关于复制到系统剪切板，使用gVim的时候默认配置下没办法直接Ctrl + C或者使用`y`命令复制到Windows系统剪切板。实际上，Vim内部存在多种寄存器，在默认配置下，`y`命令默认复制到匿名寄存器当中，要想复制到系统剪切板上就要在命令中指定寄存器。和系统剪切板相关的寄存器"+，Vim的寄存器活用配合vimscript功能强大，等读者发掘。

注意这里不是远程连接，是在本机上的Vim或者gVim。如果是Windows Terminal远程连接的话可以直接使用`Ctrl + shift + C/V`复制粘贴，不过如果启用了行号的话，复制出来比较难看。而且回车也有点怪。如果想要体验好一点可以使用cat命令获取文件内容然后再复制，或者直接使用scp命令复制文件。

注意这个引号`"`不能省略。

- `"+yy`: 复制当前行到系统剪切板。
- `"+p` : 把系统剪切板的内容粘贴出来。

Vim的10种寄存器。
```txt
1. 匿名寄存器（""）
2. 编号寄存器("0-9).
3. 小删除寄存器 ("-).
4. 命名寄存器 ("a-z).
5. 只读寄存器 (":, ".,and "%).
6. Buffer交替文件寄存器 ("#).
7. 表达式寄存器 ("=).
8. 选取和拖放寄存器("* and "+).
9. 黑洞寄存器 ("_).
10. 搜索模式寄存器 ("/).
```

可以使用`:reg`命令查看部分寄存器内容。

命令的组合可以是`数字+命令`，比如上面的`4yy`表示复制4行，还有`2w`移动两个单词的位置，还有`10dd`删除10行并保存到剪切板。数字的含义是重复执行多次命令。

也可以是`命令+命令`，比如`dw`删除当前单词，`cw`(change word)删除并编辑当前单词。

也可以混合一下比如`d2w`，删除两个单词。

其他的组合还有很多，也就不一一列举了，多翻翻手册，看看Vim帖子。

## 查找替换
建议查看vimtutor的Lesson 4。
- `/+匹配表达式`, 下一个查找位置`n`, 上一个查找位置`N`(可使用shift+n)。

这个匹配表达式也是庞杂的，和正则类似，用的时候再学也不迟，问问大模型容易解决。
- 替换如下。

节选自vimtutor
```txt
替换一个old成new               :s/old/new
替换当前行全部的old成new        :s/old/new/g
替换#和#之间的行的old成new      :#,#s/old/new/g
替换全部行的old成new            :%s/old/new/g
全部替换但是每次询问             :%s/old/new/gc
```

如果你的光标是竖线(比如Windows Terminal远程连接的，可以设置)的建议改成长方形，或者下划线，不然很难看清楚光标。如果想要更清楚可以设置`set hlsearch`高亮搜索，会将所有匹配的搜索项都标亮。

## 撤销重做

- `u`: 撤销，大写的`U`是撤销当前行的修改(undo)。
- `<Ctrl>+r`: 重做(redo)。

## 光标跳转

光标跳转是很重要的功能，特别是在阅读源代码的时候，查看定义。Vim的help手册讲过使用`<Ctrl>+]`跳转。使用`:help tags`看看tags词条。

vscode使用ctrl+鼠标左键就能实现跳转了。看似简单的操作实则不简单，背后存在语言服务器协议(Language Server Protocol, LSP)的助力。Vim原生似乎并不支持LSP，它对于词条跳转的实现是通过tags文件实现。tags文件类似一本离线词典(推荐一个离线词典软件GoldenDict)。

节选部分tags文件。
```txt
!_TAG_FILE_FORMAT   2   /extended format; --format=1 will not append ;" to lines/
!_TAG_FILE_SORTED   1   /0=unsorted, 1=sorted, 2=foldcase/
!_TAG_PROGRAM_AUTHOR    Darren Hiebert  /dhiebert@users.sourceforge.net/
!_TAG_PROGRAM_NAME  Exuberant Ctags //
!_TAG_PROGRAM_URL   http://ctags.sourceforge.net    /official site/
!_TAG_PROGRAM_VERSION   5.9~svn20110310 //
ARECORD ./kernel/net.h  165;"   d
ARP_HRD_ETHER   ./kernel/net.h  132;"   d
ARP_OP_REPLY    ./kernel/net.h  /^  ARP_OP_REPLY = 2,   \/\/ replies a hw addr given protocol addr$/;"  e   enum:__anon5ARP_OP_REQUEST  ./kernel/net.h  /^  ARP_OP_REQUEST = 1, \/\/ requests hw addr given protocol addr$/;"   e   enum:__anon5AS  ./Makefile  /^AS = $(TOOLPREFIX)gas$/;" m
Align   ./user/umalloc.c    /^typedef long Align;$/;"   t   file:
BACK    ./user/sh.c 12;"    d   file:
BACKSPACE   ./kernel/console.c  25;"    d   file:
BBLOCK  ./kernel/fs.h   51;"    d
BPB ./kernel/fs.h   48;"    d
BSIZE   ./kernel/fs.h   6;" d
BUFSZ   ./user/usertests.c  20;"    d   file:
C   ./kernel/console.c  26;"    d   file:
CC  ./Makefile  /^CC = $(TOOLPREFIX)gcc$/;" m
CFLAGS  ./Makefile  /^CFLAGS = -Wall -Werror -O -fno-omit-frame-pointer -ggdb -gdwarf-2$/;" m
```
使用ctags软件可以直接生成。Ubuntu可以直接使用apt安装，比较方便，虽然版本可能会旧一些，但是省心。支持多种语言。

```shell
$ ctags --list-languages
```

得到
```txt
Ant
Asm
Asp
Awk
Basic
BETA
C
C++
C#
Cobol
DosBatch
Eiffel
Erlang
Flex
Fortran
Go
HTML
Java
JavaScript
Lisp
Lua
Make
MatLab
ObjectiveC
OCaml
Pascal
Perl
PHP
Python
REXX
Ruby
Scheme
Sh
SLang
SML
SQL
Tcl
Tex
Vera
Verilog
VHDL
Vim
YACC
```

Verilog项目能跳转也是非常好的，目前(2024.9.6)似乎Vscode安装Verilog插件都没那么容易跳转，配置起来也是麻烦。连Vivado都不能支持一下跳转(似乎?)。

生成当前目录文件夹所有源代码文件的tags文件
```shell
$ ctags -R ./
```

tags使用非常简单，如果tags在当前文件夹的话可以是自动使用的。如果是一整个项目的时候可以使用set命令，设置tags路径。比如`:set tags=~/xv6-labs-2023/tags`，或者直接把set这一行添加到用户目录的vimrc当中去。

回到跳转。Vscode支持转到定义(ctrl+左键)，跳转下一步(alt+方向右)，跳转上一步(alt+方向左)。Vim同样支持。

- `<Ctrl>+]`: (在有tags文件的情况下)跳转到相关词条的定义。和vscode有些不同的地方，即使在注释内也可以跳转，甚至可以自己写一个单词原地跳转。
- `<Ctrl>+o`: 回到上一个光标位置(out)。
- `<Ctrl>+i`: 回到下一个光标位置(in)。

经常跳转来跳转去会忘记自己在哪个文件里面。

- `<Ctrl>+g`: 显示当前文件名和位置。

## 补全和命令

代码补全是基本素养。Vim具备基本的代码补全功能。在编辑模式下。

- `<Ctrl>+p`: 对光标所在单词进行补全。
- `<Ctrl>+n`: 对光标所在单词进行补全。区别在于previous和next，选项的方向不同。

有tags文件的话，上述命令会加上tags文件中的词条。如果没有tags文件，对于C语言，可以识别头文件，会从头文件当中寻找词条。如果是其他语言可能只能从当前文件的上下文当中寻找单词。

Vim支持多种命令。Vim内部就有许多命令。

按`:`进入命令模式后。

- `<Ctrl>+d`: 显示所有匹配前缀的命令。
- `<Tab>`: 逐个替换上匹配的命令。

关于外部命令。像VS，Vscode，IDEA都有终端可以执行编译等操作。Vim也可以执行外部命令。方法就是在`:`后面添加一个`!`。虽然用远程连接的话，多开几个窗口也不是很麻烦，不过有时候可能这样也比较方便。如果比较熟悉vimscript脚本的话，可以自定义一个运行的函数，按某些快捷键就能自动保存编译运行。Vim具有强大的键盘映射能力。当然也能在直接运行外部的测试脚本，比如用pytest写一个测试脚本。不过笔者目前还不太熟悉自动化测试，基本都是手工编译。

### 浅尝一下

打开上面写过的hello.py。
```shell
$ vim hello.py
```
修改一下。
```python
print("nihao!")
```
输入`:w`保存。输入`:!python3 h`。按`<Tab>`键得到`:!python3 hello.py`回车。
可以看到。
```txt
nihao!
Press ENTER or type command to continue
```
对于外部命令我们同样也可以使用`<Ctrl>+d`和`<Tab>`键。这样Vim就和整个Linux环境紧密联系在一起了。通过编写对应的vimscript脚本，能实现按某些键就能自动化运行。不过现在已经是2024年了，vimscript写起来还是不太方便，这个时候懂vimscript的人已经不多了，不过可以请教大模型。现在专门学习vimscript除了对编辑器有极致追求的人，效益就不那么明显了，neovim使用lua进行配置可能相对好一些，lua还能用于游戏开发。

## 保存、丢弃和另存为

- `:w`: 保存。
- `:w 路径`: 保存到指定路径，需要给一个新的文件名字。如果没有具体的路径的话会保存在当前目录。
- `:q!`: 丢弃当前的修改。

# 关于插件和其他

vim还是有很多插件的。比如ALE用来进行语法检查，还有状态栏，主题等美化的东西，NerdTree可以查看文件树。这些插件安装还是比较简单的。一般都是安装管理插件的工具比如plug-vim之类的。当然拿插件源代码(vimscript文件)也可以，自己放到合适的文件夹位置，不过得搞清楚vim插件文件的加载位置和加载顺序，还是比较头大的。

其他细节还要等读者慢慢探索。比如用Vim编辑makefile的时候，必须要使用`Tab`键。上面配置的`tabexpand`就不妙了，都替换成空格。这时候可以在Makefile的头几行添加模式行，禁用`tabexpand`，模式行是在打开这个文件的时候默认执行的命令。

# 后记

不得不说，Vscode还是太方便了。装插件就能实现代码补全，简单的配置就能高度可用，点一个按键就能自动编译运行，还能debug。学习使用Vim给我有种打破对代码编辑工具的刻板认知的感觉，重新解构编辑器。从最基本的编辑，到代码补全，语法高亮，编译运行和调试，原本“浑然一体”的编辑器被解构成了一个个模块，再逐个组装起来又成了一个完整的编辑器。开发一个高度可用的编辑器也是相当不容易的，需要支持这么多的功能模块。

# 参考资料

1. Wikipedia.Vim_(text__editor)
2. Vimtutor
3. 大模型和互联网