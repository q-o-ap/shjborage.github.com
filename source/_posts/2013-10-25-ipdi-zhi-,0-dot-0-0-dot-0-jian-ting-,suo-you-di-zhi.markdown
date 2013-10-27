---
layout: post
title: "IP地址，0.0.0.0 监听，所有地址"
date: 2013-10-25 01:07
comments: true
categories: Network
---

曾经在使用PonyDebugger时出现，socket连接老是被拒绝的情况。
PD可以自动连接，使用Boujour进行发现，很是方便。但是解析到了IP地址后，因为Server的监听有问题，老是connection refused. 笨笨的我还是没想到用**0.0.0.0**这个来监听所有IP地址，解决这个问题。

不能再出现同样的问题了，不用怕被别人笑话，哈哈