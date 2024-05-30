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
```
Hello, world!
请按任意键继续. . .
```

自主查阅资料思考问题：

- 问题1：`@echo off`删去后会怎样？
- 问题2：windows和Linux的`echo`有什么区别？
- 问题3：`pause`有什么作用？

## Try2 