title: 记录一些我在Ubuntu下的软件
date: 2015-03-06 09:25:04
categories: Develop
tags: Ubuntu
---

我使用的版本是`Ubuntu 14.04 LTS`，比较常见的如git这些就不说了嘛，说一下稍微冷门的


###shadowsocks

由于众所周知的原因，有些网站无法访问或者访问很慢。相较于VPN，优势在于比较灵活。作者的README写得非常详细了，不需要多解释什么了。
https://github.com/librehat/shadowsocks-qt5/wiki。
<!-- more --> 

----------


###deepin scort（深度截图）
如果你有写博客的习惯那就肯定离不开截图软件了，windows平台下最便捷最强大的截图软件是QQ，deepin scort在功能上则很像QQ截图。

安装方法，目前版本是2.0

    wget http://packages.linuxdeepin.com/deepin/pool/main/d/deepin-scrot/deepin-scrot_2.0-0deepin_all.deb
    sudo dpkg -i deepin-scrot_2.0-0deepin_all.deb
    
    //可能提示缺少python依赖
    sudo apt-get install python-xlib
    //终端下启动
    $ deepin scort
    
Ubuntu 系统设置里可以绑定快捷键，我依旧绑定了ctrl+alt+A。但是在多屏的时候会有点问题，还是推荐在终端里运行。

![图片描述][1]


----------


###命令行词典工具

- [ydcv][2]
- [pydict][3]

翻译文档的时候比较懒，希望直接用命令行就能查询出想要的单词。这两个小脚本就派上用处了。老牌的翻译软件如Stardict用户体验也停留在n年前，直接弃用。

![图片描述][4]


----------


###Geany

Ubuntu下比较完美的编辑器。比gedit强大，同时又完美支持中文输入。这点上比Sublime Text好。
我使用的字体是Ubuntu MONO Bold

![图片描述][5]


----------


###Ubuntu Tweak
作者已经停止开发了，现在主要用于清理缓存


----------


###netifera
局域网工具，可以查看局域网所有用户


----------


###sysv-rc-conf
这工具名字实在不好记呀，命令行图形化配置服务的运行级别，很方便的能管理各种服务。
Run-level configuration for SysV like init script links

![图片描述][6]


----------


###xclip
命令行复制工具


----------


###Airoscript-ng

贴一段官网介绍：

>Airoscript-ng makes aircrack-ng use easier and faster!
Either you've lost your wireless key or you've been contracted as pentester Airoscript-ng is your best choice, your tool, your weapon.


----------


###Dbeaver

图形化数据库管理工具 [here][7]


  [1]: http://segmentfault.com/img/bVkZ9Z
  [2]: https://github.com/felixonmars/ydcv
  [3]: https://github.com/np-csu/pydict
  [4]: http://segmentfault.com/img/bVkZ95
  [5]: http://segmentfault.com/img/bVk0aj
  [6]: http://segmentfault.com/img/bVkZ92
  [7]: http://dbeaver.jkiss.org/download/
