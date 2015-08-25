title: Ubuntu下无线网卡禁用一次之后无法再开启的问题
date: 2014-03-20 20:53:20
categories: 好人修电脑
tags: [Ubuntu,网卡,你是一个好人]
---

##症状描述

----------

Ubuntu下使用默认网络管理禁用无线网卡后无法再次启用，无线网络管理处显示该设备已被硬件设备禁用。切换到其他系统，比如Windows也显示已经禁用。
Ubuntu版本12.04LTS
<!-- more --> 
##解决方法

----------

查看无线网卡是否被屏蔽

	sudo rfkill list

如果有no的，继续往下看吧，如果都是yes，请自行google其他方法，不过死马当活马医，也可以使用一下以下方法。
解除屏蔽

	sudo rfkill unblock all

为了防止这样的事情再发生,推荐使用`wicd`来管理网络。
卸载自带的网络管理

	sudo apt-get remove network-manager-gnome
	sudo apt-get update
	sudo apt-get install wicd
	sudo wicd-client

不过感觉`wicd`的界面没有`network-manager-gnome`好看,好处是在命令行界面也能连接无线网络


