---
layout: post
title: "Mac OS X添加PATH环境变量"
date: 2013-10-24 23:53
comments: true
categories: Mac
tags: Mac
---

解决办法:
sudo vim /etc/paths

将路径添加到里面去， 一行一个路径

注意：即便添加成功，未必运行成功；在制定路径下得脚本必须具是executable, 否则就会被在搜索时被忽略。
sudo chmod +x XXX

而在Ubuntu下，则只需要修改/etc/.profile或者 ~/.profile或~/.bashrc等修改

如转载文章，请注明本文链接： http://www.32133.com/skills/575.html
