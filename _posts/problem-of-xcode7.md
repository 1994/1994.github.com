title: 使用xcode7 beta开发cocos2dx的一个小问题
date: 2015-06-12 15:23:00
categories: Develop
tags: [Xcode7,cocos2d-x]
---

今年的`WWDC2015`对于开发者来说真是好事，「大快所有开发者的大好事」、让开发者「真的笑、笑出声」:-)。其中一个特性是`xcode`不需要证书就能真机调试了，这意味着调试这块不用再向苹果交保护费了，于是我赶紧升级到最新的beta版来测试下。
<!-- more --> 
首先需要说明的是真机调试需要`xcode`上的Apple ID和真机的Apple ID一致。新建一个简单的cocos2dx工程，build and run，出现了下面这个问题：
![图片描述][1]
为了方便被搜索引擎收录，我把这异常打一遍：

    Command /Applications/Xcode-beta.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/clang failed with exit code 11

![图片描述][2]

解决方法也很简单，在`Building Settings`里搜索pch，然后删除掉如下内容即可。

  [1]: http://segmentfault.com/img/bVmklK
  [2]: http://segmentfault.com/img/bVmklO