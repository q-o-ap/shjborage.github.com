---
layout: post
title: "JavaWeb开发 eclipse myEclipse JSP编辑卡死问题解决"
date: 2013-06-03 15:16
comments: true
categories: JavaWeb
---

这几天用lomboz3.3编辑JSP文件时，输入<%@总是卡死，这是因为lomboz在我们输入<%@时，lomboz在后台跟www.w3.org网站自动连接（IP：128.30.52.166：80），我是用WPE监听发现的。
 
解决办法：
######Windows:

打开C:\WINDOWS\system32\drivers\etc\下的hosts文件，

#####Linux等:

/etc/hosts

在其中加入 127.0.0.1   www.w3.org
OK,解决了。