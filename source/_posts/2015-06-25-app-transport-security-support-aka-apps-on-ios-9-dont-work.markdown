---
layout: post
title: "App Transport Security support aka apps on iOS 9 don't work"
date: 2015-06-25 10:12
comments: true
categories: iOS
tags: iOS iOS9
---

虽然不想说，但还是很久没更新了。<br>
最近被El caption折腾了，虽然是有史以来最好用的beta版，但第二个beta被害了。

几个重要的点   
1. Cornerstone 罢工（2.7.10）   
2. iStat Menus 5（正版最新）罢工   
3. beta2上Xcode 6.3.2 直接也不让用了 - -！   

这些。。有些不能接受

不过我是这样解决的    
1. svn 直接命令行    
2. iStat Menus 放弃，反馈给官方等更新（把系统设置又改了一圈）    
3. Xcode直接使用beta版凑合用（要上线就找同事咯～）    

不过问题总是会找上门来：

__*iOS9的模拟器一直上不了网，但模拟器里的Safari是可以联网的*__    
我勒个去啊

查了好久却一直没啥进展，可能是对网络安全了解较少，最好发现是协议升级，需要在app上进行一些配置才可以。原文如下：
>
iOS 9 (may) force developers to use App Transport Security exclusively. I overheard this somewhere randomly so I don't know whether this is true myself. But I suspect it and have come to this conclusion:
>
The app running on iOS 9 will (maybe) no longer connect to a Meteor server without SSL.
>
This means running meteor run ios or meteor run ios-device will (probably?) no longer work.
>
In the app's info.plist, NSAppTransportSecurity [Dictionary] needs to have a key NSAllowsArbitraryLoads [Boolean] to be set to YES or Meteor needs to use https for its localhost server soon. (From Page 26 http://devstreaming.apple.com/videos/wwdc/2015/711y6zlz0ll/711/711_networking_with_nsurlsession.pdf?dl=1)
>
This is what an error may look like. Of course this is at best, a guess:
>
The resource could not be loaded because the App Transport Security policy requires the use of a secure connection.
The best possible fix is to get Meteor to use a SSL server in dev mode.
>
Oh the hassle! Also again, I overheard this from a loud third party.

引用：<https://github.com/meteor/meteor/issues/4560>

附：真机调试也还有问题，是部分库造成的，这个还需要研究下。