title: 使用Weinre调试webapp
date: 2014-03-19 23:20:20
categories: Develop
tags: [weinre,webapp]
---


##前言

移动web的调试一直是个难题，前期可以使用模拟器来协助调试，但到了真机调试阶段就让人非常头痛。而 Weinre就是解决这难题的利器。 Weinre的本意是Web Inspector Remote，它是一种远程调试工具。功能与Firebug、Webkit inspector类似，可以帮助我们即时更改页面元素、样式，调试JS等。这篇文章是我之前发表在oschina的博文，[原始链接](http://my.oschina.net/reeco/blog/209829)

##weinre的安装

首先下载nodejs，我的环境是debian，在[nodejs官网](http://nodejs.org/download/)下载源码包，解压并移动。

![](http://7xawrk.com1.z0.glb.clouddn.com/re225310_OeIo_853361.png)

	tar xvf node-v0.10.26.tar.gz
	mv  node-v0.10.26 /opt/nodejs  //移动到/opt/nodejs
	
	
<!-- more --> 
![](http://7xawrk.com1.z0.glb.clouddn.com/re225311_dKWe_853361.png)

进入目录并检查环境

	cd /opt/nodejs
	./configure

Nodejs安装需要python, 如果python版本太低，请安装合适的python版本，建议使用python-2.7的版本,还需要GCC/G++，如果你计划在Node.js中启用网络加密，OpenSSL的加密库也是必须的。该加密库是`libssl-dev`，可以通过`apt-get install libssl-dev`等命令安装。

###安装nodejs

	sudo make
	sudo make install

安装的过程会比较久，我的渣机子足足运行了一个小时。

要保证user有sudo权限，user不在sudo组的话，参考以下：

首需要切换到root身份

	su - //注意有- ，这和su是不同的，在用命令"su"的时候只是切换到root，但没有把root的环境变量传过去，还是当前用户的环境变量，用"su -"命令将环境变量也一起带过去，就象和root登录一样

	visudo //切记，此处没有vi和sudo之间没有空格
	
 - 移动光标，到最后一行
 - 按a，进入append模式
 - 输入 `your_user_name ALL=(ALL)  ALL`
 - 按Esc
 - 输入“:w”(保存文件)
 - 输入“:q”(退出)


 这样就把自己加入了sudo组，可以使用sudo命令了

![](http://7xawrk.com1.z0.glb.clouddn.com/re225311_NnD7_853361.png)

###通过npm安装Weinre

	npm -g install weinre

![](http://7xawrk.com1.z0.glb.clouddn.com/re225311_NnD7_853361.png)

##weinre的启动

Weinre默认路径为`/usr/local/lib/node_modules/weinre/weinre`，初始端口为8080，进入，装目录：

	weinre --boundHost -all-

就可以启动weinre，也可以指定端口参数：

	weinre --boundHost -all- --httpPort 8081

![](http://7xawrk.com1.z0.glb.clouddn.com/re225312_UYo9_853361.png)

如果是外网的话还需要在路由器里设置端口映射。

##weinre的使用


webapp的启动页面加上下面的js引用：

	<script type="text/javascript">http://192.168.1.101:8081/target/target-script-min.js#{app标识}</script>

其中{app标识}是webapp的唯一标识，任意字符串。

启动webapp之后，访问`http://192.168.1.101:8081/client/#{app标识}`，注意，这里的http后面的网址需要和script中的一致，连接上之后可能在首页上不显示客户端已连接，直接访问调试的网址即可。

![](http://7xawrk.com1.z0.glb.clouddn.com/re230513_78nO_853361.png)

看到这个界面应该很熟悉吧，尤其是上面的几个面板

1. 面板切换，用过 Chrome 或者 Safari 开发者工具的对这个界面肯定很熟悉。
2. 连接到调试服务器的页面，即可以调试的页面。
3. 连接到调试服务地的客户端，当前只有一个。
4. 调试服务器属性，绑定的端口和调试服务器能够响应式的 IP 地址列表。

我这里调试的是一个phonegap项目，还是比较方便的

----

扩展阅读：http://people.apache.org/~pmuellr/weinre/docs/latest/Running.html
