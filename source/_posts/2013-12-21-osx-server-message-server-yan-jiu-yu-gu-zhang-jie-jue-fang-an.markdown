---
layout: post
title: "OSX Server - Message Server 研究与故障解决方案"
date: 2013-12-21 00:50
comments: true
categories: Mac
tags: Mac Tools
---

OSX Server是个出色的小团队服务端解决方案，前两个月发邮件说这个可以让我使用兑换券免费下一个，一定不能错过的机会，平时可是会卖很贵的啊

特分享一下使用的相关事项吧

<!-- more -->

###简介

详见[官方介绍](http://www.apple.com/cn/osx/server/)

###使用过的服务

*	Message Server
*	DNS
*	FileShare
*	WebServer
*	Wiki
*	。。。


可以说每个都能解决我们的某些需求，但是这些东西呢，没有OSX Server我们一样能找到替代方案。 但是苹果就是能在易用性上能帮我们做一些事。比如这个Wiki就比较简单实用

但是呢,有些也做的不是太好，比如Message Server

###Message Server

####用户

最开始在10.8上面直接搞上了，但是不知道怎么建用户（第一次玩Jabber，XMPP）
帮助文档不太好找，上链接，有写用户的使用。

https://help.apple.com/advancedserveradmin/mac/3.0/#apd447EC2A9-7F7E-49B1-AEDD-144C189926C7
见这段*Understand Messages screen names*

####登录

无论如何都登录不了，这个更是愁人。。。
也在上面的帮助文档里有说明，见这段*Server-to-server federation*

需要在设置中把自己的域名也加进去（最好配置DNS，否则需要自己改hosts文件）

####还是登录不了。。

自己的Mavericks（10.9）还是登录不了，可能是域名设来设去的吧，无论如何也搞不定，查Google也不太好找，找了一些检索词，最后还是放弃，自己搞。
看文档，日志会写入**system.log**, 打开Console，看到报错如下:

*SASL callback for non-existing host"* 

Google之，解决方案如下

https://discussions.apple.com/message/23948580#23948580

这里面说的两个文件的路径还不太对，在这个目录下找那俩文件修改相关域名即可

```
/Library/Server/Messages/Config/jabberd
```

###Message Server OK

可以使用Mac 上面的Message直接登录，团队其它直接使用Server的DNS，这样就不用绑hosts了，然后使用rooms聊天室，这样系统内置了Message，而且还会比较及时的通知到，非常方便。

*远离QQ吧。*  Good night!