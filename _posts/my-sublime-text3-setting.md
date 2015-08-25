title: 我的sublime text设置
date: 2015-03-04 17:10:35
categories: Develop
tags: Text
---

#####前言
Sublime Text3 是一款非常优秀的跨平台编辑器，在此简单记录下我的Sublime Text3的配置。
#####安装
首先打开[Sublme Text官网！废话:)](http://www.sublimetext.com/3)
安装方法非常傻瓜式，下载下来双击就行了。
ubuntu 64 bit的话可以安装在终端下:

    wget http://c758482.r82.cf2.rackcdn.com/sublime-text_build-3065_amd64.deb
或者通过源安装：


    sudo add-apt-repository ppa:webupd8team/sublime-text-3 
    sudo apt-get update $ sudo apt-get install sublime-text-installer

<!-- more --> 
#####开始使用
![界面](http://ww4.sinaimg.cn/large/005yyi5Jjw1em67df3n1zj30qd0hfgmk.jpg)


工欲善其事必先利其器,sublime text拥有很多酷炫的插件。下面介绍一下我安装的几个插件
######安装package control
package control类似于debian的apt-get，redhat的yum，有了它能极大得简化插件的安装。
ctrl+~（Esc下面那个键）同时按住，弹出一个输入框，粘贴下面代码，回车。我使用的版本是sublime text3.

    import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())

再贴一个sublime text2的备忘：

    import urllib2,os; pf='Package Control.sublime-package'; ipp = sublime.installed_packages_path(); os.makedirs( ipp ) if not os.path.exists(ipp) else None; urllib2.install_opener( urllib2.build_opener( urllib2.ProxyHandler( ))); open( os.path.join( ipp, pf), 'wb' ).write( urllib2.urlopen( 'http://sublime.wbond.net/' +pf.replace( ' ','%20' )).read()); print( 'Please restart Sublime Text to finish installation')
![](http://ww1.sinaimg.cn/large/005yyi5Jjw1em67igboj0j30qd0h4jt4.jpg)



最近package control的package仓库貌似被墙了，这里提供一个**解决方法**：


修改hosts文件，添加上一句


		50.116.34.243 sublime.wbond.net



安装好之后，Preferences菜单下看到Package Settings和Package Control两个菜单了，点击Package Control，install package ，输入相应关键字，就能开始安装插件了。
######**Emmet**
撸HTML、CSS必备的

######**Color Highlighter&&Color Picker**
这两个插件需要同时安装，然后就能显示十六进制的颜色的。但颜色这块没有webstorm的赞。

######**Prettify**
这也是好东西，HTML、CSS、JS、JSON.....Ctrl+Shift+H 一键就能格式化了

######**ConvertToUTF8**
sublime Text不支持中文显示的，不知道这算不算一种歧视

以上这几个是我认为必备的插件，其他的各位根据自己需要自行搜索并安装吧。
插件不需要安装太多，太多了可能会出现快捷键冲突的问题。


#####配置简单的IDE
######Java

新建一个runJava.bat,放在JAVA_HOME的bin内，比如我的就在C:\Program Files (x86)\Java\jdk1.8.0\bin内，内容如下：


    @ECHO OFF 
    cd %~dp1 
    ECHO Compiling %~nx1……
    IF EXIST %~n1.class ( 
    DEL %~n1.class 
    ) 
    javac %~nx1 
    IF EXIST %~n1.class ( 
    ECHO ———OUTPUT———
    java %~n1 
    )

用winrar打开sublime text的配置目录，在%安装目录%/package/Java.sublime-package

修改内容为


    {
      "shell_cmd": "runJava.bat \"$file\"",
      "file_regex": "^(...*?):([0-9]*):?([0-9]*)",
      "selector": "source.java",
      "encoding": "GBK"
    }

其实写到这里不要想往下写了，用编辑器来写项目实在是有点蛋疼。我还是老老实实用eclipse之流吧。逃：）
#####其他技巧
######分屏
    Alt+Shift+1/2/3/4/5/8/9
或者View->Layout
![](http://ww1.sinaimg.cn/large/7001c874gw1em68wzi2ipj21hc0sy47e.jpg)
######记不住快捷键怎么办
其实只要记住一个就行了，ctrl+shift+P，然后搜索想要的命令

#####扩展阅读
[https://sublime.wbond.net/installation#st3](https://sublime.wbond.net/installation#st3)
<br>
[http://www.sublimetext.com/forum/](http://www.sublimetext.com/forum/)
<br>
[https://github.com/SublimeText](https://github.com/SublimeText)
