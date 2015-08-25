title: VSCode初体验
date: 2015-04-30 22:00:00
categories: Develop
tags: [编辑器,VSCode]
---

Microsoft 今天在其 Build 开发者大会上正式宣布了 Visual Studio Code 项目：一个运行于 OS X，Windows 和 Linux 之上的，针对于编写现代web和云应用的跨平台编辑器。

作为编辑器控的我，得知消息后立马下载体验了一下。Windows上优秀的编辑器实在太多了，`Sublime Text`，`EditPlus`，`Notepad++`......还有诸如国产的`EverEdit`等后起之秀。所以这次我这次把测评的环境放在了编辑器相对匮乏的Linux桌面上。
<!-- more --> 
##环境&安装

 - Ubuntu 14.04 LTS
 - Visual Studio Code 0.1.0

主要对比对象是`Sublime Text3`


        wget http://download.microsoft.com/download/0/D/5/0D57186C-834B-463A-AECB-BC55A8E466AE/VSCode-linux-x64.zip
        
        //注意不要使用归档解压会报错
        unzip  unzip VSCode-linux-x64 -d VS
        
        //双击VS里的Code就能运行了


##颜值

![VSCode][1]

可以看到VSCode颜值不算太糟糕，绿色的注释散发着一股浓浓的VS的风格，Theme里一共两款主题可以选择，另外一款是白色主题。题外话，我最喜欢的主题是Sublime Text的`Monokai`。

##性能

总体来说输入的体验比Sublime Text3稍微要差一点，但是比同类WEB IDE `ATOM`，`Brackets`要快太多，ATOM、Brackets已经迭代很多个版本了，VSCode基于ATOM SHELL的，估计ATOM要哭晕在厕所。看到一些网友的测试，在打开大文件上，VSCode已经秒杀了`Sublime Text3`。

##特性

###智能提示

VSCode提供了强大的自动补全、悬浮提示、定义跳转等功能，支持以下语言：

    C++, jade, PHP, Python, XML, Batch, F#, DockerFile, Coffee Script, Java, HandleBars, R,Objective-C, PowerShell, Luna, Visual Basic, Markdown

我测试了下在Javascript、Typscript上体验不错，HTML还支持Angular标签，悬浮提示很详细包括了注解，但是试了下C#貌似没有什么效果，不知道是不是需要特殊的环境。不管怎样，在某些语言上的智能提示已经比其他的同类编辑器已经强太多了，可以和一些IDE媲美。

![enter image description here][2]

下面贴几张官网的示例图片:

参数提示：
![enter image description here][3]

定义跳转：
![enter image description here][4]

引用提示：
![enter image description here][5]

方法定位：
![enter image description here][6]

还有其他很酷炫的功能我没测试，大家[官网][7]看吧。

###Markdown

在Linux桌面上，好用的Markdown编辑器可以说没有，`ReText`和记事本一样简陋，Sublime Text3虽然可以装插件支持，但是体验不是很好，不支持中文。因此我一直使用的在线Markdown代替。

这回VSCode支持Markdown重新让我看到了点希望。快捷键`ctr+shift+v`预览，可以看到这个布局还是非常人性化的。

![enter image description here][8]

但是缺点也很明显，首先中文支持不好，编辑器里的中文输入可以改，但是预览还是出现口口，目前找到解决方法。还有不支持快捷键输入，那种像写代码般的快感没有了。没有能自定义CSS的功能，不管在哪种Theme下，`>` 代码高亮都看不出有什么效果。

###版本控制

自带了一个git工具，并且放在了一个比较显要的位置上，不过功能不是很全，只能`commit`等几个操作。自带了类似于git diff的文件比较功能：

![enter image description here][9]

###Debug

Debug需要MONO，所以就没进行测试。详情大家看[官网][10]吧。

##缺陷

###中文支持

默认的字体是不支持中文的，输入中文的时候会出现口口。需要设置一下字体，我使用的是文泉驿，思源也行。

没安装的首先安装这个字体。

    sudo apt-get install fonts-wqy-microhei fonts-wqy-zenhei
    File -> Preference -> User Settings
    //在右侧添加一句：
    "editor.fontFamily": "WenQuanYi Micro Hei Mono"

不过这只能解决编辑器内的中文乱码问题，其他的比如标题栏，markdown预览，该口的还是口。对了还有一点需要注意的是输入法需要是`Fctix`或者基于`Fctix`的。

Sublime Text3同样有这问题，事实上Sublime Text3全平台对于中文的支持都不是很好。Linux桌面上的解决方法也是[奇技淫巧][11]。

###插件化

不过插件化已经提到议程上了，以微软的实力实现这个不难。

###Markdown

缺陷在上面已经提到了

###设置

用户设置是直接以JSON形式出现了，虽然说鼠标悬浮上去会看到详细的解释，但还是没有图形化来的简便，而且没有搜索的功能，想要搜索还得以文本的形式复制出来，修改起来略费劲。

##结论

总体而言，VSCode表现出来的潜力还是不俗的，毕竟还是个预览版，我对接下来的版本比较看好，至少比`Brackets`要好吧。希望Sublime Text的作者能够更加上心一点，能解决中文问题那就最好了，喜欢Sublime Text3的童鞋们可以看我这篇博文[《我的Sublime Text3设置》][12]。

最后，人生苦短，我用`geany`。

##参考

https://code.visualstudio.com/Docs
http://www.zhihu.com/question/29984607


  [1]: http://7xawrk.com1.z0.glb.clouddn.com/c08cce9034f37cc93fc026bba9a9e326_b.jpg
  [2]: http://7xawrk.com1.z0.glb.clouddn.com/b3041588c25e63c108d34519c1ed7435_b.jpg
  [3]: https://code.visualstudio.com/Content/images/editing_evolved_parameterhints.png
  [4]: https://code.visualstudio.com/Content/images/editing_evolved_ctrl_hover.png
  [5]: https://code.visualstudio.com/Content/images/editing_evolved_reference_info.png
  [6]: https://code.visualstudio.com/Content/images/editing_evolved_symbol.png
  [7]: https://code.visualstudio.com/Docs/editingevolved
  [8]: http://7xawrk.com1.z0.glb.clouddn.com/d01756018f5c5bb172180a81522badec_b.jpg
  [9]: http://7xawrk.com1.z0.glb.clouddn.com/ab4461ceb4d7121a0dc7276a0e47c0c4_b.jpg
  [10]: https://code.visualstudio.com/Docs/debugging
  [11]: http://my.oschina.net/tsl0922/blog/113495
  [12]: http://reecoblog.info/2015/03/04/my-sublime-text3-setting/