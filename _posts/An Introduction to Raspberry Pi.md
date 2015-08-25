title: 树莓派快速入门指南
date: 2015-03-09 12:45:00
categories: Develop
tags: [Raspberry,Linux,Debian]
---

###前言


----------


接触树莓派已经有一年多的时间了，主要用于Linux服务器。前一段时间树莓派2发售了，树莓派又小火了一把。现在我介绍一下我的经验，希望对于各位读者有多帮助吧。
<!-- more --> 
###准备

----------


 - 树莓派
 - 路由器
 - 网线
 - 电源
 - SD
 - 计算机

>不用路由器也可以，参考[这篇文章][1]。不过还是建议用吧，能省下不少工作。

###刻录镜像
 


----------


 1. 在[这里][2]下载喜欢的镜像，我选择的是RASPBIAN，Debian大法好！
 2. 下载[Win32DiskImager][3]
 3. 插上SD卡并刻录
![raspberry-pi-diskimager.png][4]

###组装并启动

----------

这块我想不用仔细讲了吧，看到树莓派两个绿灯亮起则表示启动成功。



###SSH连接


----------


>如果不了解SSH是什么，点击[这里][5]

树莓派默认已经启动了ssh服务


 - 获取树莓派ip地址，如果用了路由器的话这点轻而易举，进入管理界面就能找到。比如我的是`192.168.1.104`

    ![图片描述][6]

 - 绑定静态IP，方便以后管理。绑定静态地址有很多种方法，我这里选择的是最偷懒的一种，比如通过修改[interface][7]。建议分配一些比较偏的IP，以免和DHCP分配的发生冲突。

    ![图片描述][8]

 - 默认用户名为pi，默认密码为raspberry

        ssh pi@192.168.1.104
    
 
 - 如果前面一切正常就能看到`pi@raspberrypi ~ $` 


    ![图片描述][9]

###快速设置


----------

    sudo raspi-config

![图片描述][10]
如果你的SD卡大于4G，建议选择第一项Expand Filesystem，其他的视情况选择吧。
为了避免出现乱码的麻烦，推荐使用英文系统。

###安装应用


----------
####搜索最快的源
令人鼓舞的是，国内很多高校已经开通了树莓派的镜像源，据我的不完全统计：

 - [浙江大学][11]
 - [重庆大学][12]
 - [华中科技大学][13]
 - [中山大学][14]

下面使用我们的小工具，自动查找最快的源：

    sudo apt-get install apt-spy
    sudo apt-get update
    sudo apt-spy -d wheezy -a Asia

也可以手动添加以上的地址到`/etc/apt/sources.list`

####安装tasksel

    sudo apt-get install tasksel
    sudo tasksel
    
![图片描述][15]

可以很方便得安装常见服务与应用

###使用VNC显示图形界面


----------
>这部分不太推荐，除非你是特别想看看树莓派的桌面，因为这不仅卡还没什么意义

    //安装
    sudo apt-get install tightvncserver
    
    //运行
    vncserver :1 
    
    //第一次运行VNC会让你输入密码，记住它
    
使用Remmia之类的客户端就能直接连接树莓派了，Windows自带的远程桌面能直接连接。

###使用ssh key登录


----------


>从这部分开始为进阶内容




每次登录都输入密码既麻烦又不安全，使用ssh key代替密码登录。

####生成密钥

    ssh-keygen -t rsa
    
然后会出现，强烈建议你使用默认，也就是只要Enter就好了

    Enter file in which to save the key (/Users/you/.ssh/id_rsa): [Press enter]

出现类似以下信息就创建好了

    Your identification has been saved in /Users/you/.ssh/id_rsa.
    # Your public key has been saved in /Users/you/.ssh/id_rsa.pub.
    # The key fingerprint is:
    # 01:0f:f4:3b:ca:85:d6:17:a1:7d:f0:68:9d:f0:a2:db
    
####将key添加到ssh-agent
  - 确保ssh-agent是可用的

        $ eval "$(ssh-agent -s)"
        Agent pid 12190
    
  - 打印出pid信息则表示正常

        ssh-add ~/.ssh/id_rsa
     
####将公钥添加到远程服务器
>这里的远程服务器指的就是pi

 - 首先检查一下本地的密钥

        ls -al ~/.ssh
  


    可以看到先前创建的id_rsa,id_rsa.pub 



        出现如下信息：
        total 24
        drwx------  2 reeco reeco 4096  3月  9 18:59 .
        drwxr-xr-x 55 reeco reeco 4096  3月  9 18:59 ..
        -rw-rw-r--  1 reeco reeco   47  3月  9 18:59 config
        -rw-------  1 reeco reeco 1679  3月  4 16:10 id_rsa
        -rw-r--r--  1 reeco reeco  399  3月  4 16:10 id_rsa.pub
        -rw-r--r--  1 reeco reeco 2876  3月  9 16:28 known_hosts
        
 - 我们要做的就是将id_rsa.pub 复制到树莓派里,推荐的做法是：

        ssh-copy-id remote_host
        
 - 如果你不甘寂寞，想挑战hard模式也可以：
        
        //树莓派下：
        sudo mkdir ~/.ssh
        
        //修改权限，不然无法写入
        sudo chmod 777 ~/.ssh
        
        //计算机下：
        cat ~/.ssh/id_rsa.pub | ssh pi@host 'cat - >> ~/.ssh/authorized_keys'
        
        //将权限修改回来
        chmod 600 ~/.ssh/authorized_keys && chmod 700 ~/.ssh/

####测试连接
    ssh pi@host
    pi@raspberrypi ~ $
 awesome!成功了！
####修改ssh config再次简化ssh连接
不需要密码了还觉得每次输入一大串麻烦，有的时候记不住IP地址，还有更简单的方法吗？当然有了，往下看：
  

     touch ~/.ssh/config
     vim ~/.ssh/config
     
     //INSERT 以下内容:
     
       Host pi
       HostName host_ip
       User pi
       Port 22
接着
    
    ssh pi
    
也能成功连接上，是不是很cool！

###参考更多


----------


[树莓派Raspberry Pi上手报告][16]
[不可小觑！树莓派16个酷炫玩法][17]
[Raspberry Pi Beginers][18]
[Raspberry on github][19]


  [1]: http://tieba.baidu.com/p/2632749888
  [2]: http://www.raspberrypi.org/downloads/
  [3]: http://www.softpedia.com/get/CD-DVD-Tools/Data-CD-DVD-Burning/Win32-Disk-Imager.shtml
  [4]:http://segmentfault.net/img/bVk1iJ
  [5]: http://zh.wikipedia.org/wiki/Secure_Shell
  [6]: http://segmentfault.net/img/bVk1i7
  [7]: https://www.linode.com/docs/networking/linux-static-ip-configuration
  [8]: http://segmentfault.net/img/bVk1jc
  [9]: http://segmentfault.net/img/bVk1js
  [10]: http://segmentfault.net/img/bVk1jw
  [11]: http://mirrors.zju.edu.cn/
  [12]: http://mirrors.cqu.edu.cn/
  [13]: http://mirrors.hust.edu.cn/
  [14]: http://mirrors.hust.edu.cn/
  [15]: http://segmentfault.net/img/bVk1ki
  [16]: http://www.leiphone.com/news/201406/raspberry-pi-hands-on.html
  [17]: http://www.ithome.com/html/digi/121079.htm
  [18]: http://www.raspberrypi.org/forums/viewforum.php?f=91
  [19]: https://github.com/search?utf8=%E2%9C%93&q=raspberry
