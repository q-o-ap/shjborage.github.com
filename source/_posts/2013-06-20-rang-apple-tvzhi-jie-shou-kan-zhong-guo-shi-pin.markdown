---
layout: post
title: "让Apple TV直接收看中国视频"
date: 2013-06-20 14:09
comments: true
categories: other
---


####工作原理：
通过程序将视频网站的HTML转换成XML给Apple TV,于是ATV就能收看贵国视频网站了。

本程序给Apple tv访问的网址 : http://4.appletv3.sinaapp.com/index.php(其他设备浏览器无效) 

此方法许多人都设想过，但是TTVast是第一位做出并共享者！由于有了TTVast的工作，我们后来者的工作就轻松许多，感谢TT!为了方便新研究的朋友，特分享我的源码，链接在页面底部。

TT的微博：http://weibo.com/u/1400101157
http://www.ottnt.com/ Apple TV论坛 

<!-- more -->

####没有自己的DNS

　　　只需修改Apple TV的DNS（其它网络设置默认），然后地区选择美国，
　　　即可将Apple TV内置网址指向一个新的网站，达到通过Apple TV直接收看贵国视频的愿望。
修改DNS如下：
```
180.153.225.136 电信服务器，适合于南方电信的用户使用。TTVast提供
210.129.145.150 适合部分联通用户，以及海外用户使用。TTVast提供
```
　　用私人DNS会轻易被他伪装的银行、淘宝等网站来欺骗你，为了安全，请ATV用户使用上面靠谱的。
　　请勿用于Apple TV之外的任何设备（如：手机、PC、路由器等），否则后果自负。
　　
####拥有自己的DNS
将 http://trailers.apple.com/appletv/ 路径下的 index.xml 及 application.js转向下面网址的对应文件
```
http://180.153.225.136:8000/index.xml ttvas提供
http://210.129.145.150:8000/index.xml ttvas提供
http://4.appletv3.sinaapp.com/index.php Missde提供 - 目前测试中..
```

本程序：Apple TV DNS PHP代码下载：
https://code.google.com/p/appletv/downloads/list 

获取音悦台MV下载地址 http://huoqu.sinaapp.com/video/