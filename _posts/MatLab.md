title: MatLab7在Windows7，Windows8中安装问题摘要
date: 2014-03-20 18:30:00
categories: 好人修电脑
tags: [MatLab,Windows,你是一个好人]
---

具体的错误代码就不发了，具体是两点：

###修改改环境变量：
新建变量名：`BLAS_VERSION`
变量值：`D:\matlab7\bin\win32\atlas_Athlon.dll`
在你的安装文件夹里搜索atlas_Athlon.dll，记好它的路径，
用`（路径名）\atlas_Athlon.dll`作为变量值
<!-- more --> 


###更改程序兼容性：

Matlab图标上点右键，属性，兼容性，Vista SP2兼容性运行

至于为什么是Vista SP2，我也不知道。选其他的诸如XP SP3这些都不行
