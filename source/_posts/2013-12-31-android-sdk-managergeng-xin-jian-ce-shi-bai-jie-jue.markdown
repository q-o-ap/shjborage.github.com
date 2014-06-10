---
layout: post
title: "Android SDK Manager更新检测失败解决"
date: 2013-12-31 13:39
comments: true
categories: Android
tags: Android Tools
---

好久没弄Eclipse开发Android了，最近弄了个天猫的盒子，就拿出来玩玩，没想到新的SDK下不下来，悲剧。

###Ｑ：

Failed to fetch URL https://dl-ssl.google.com/android/repository/repository.xml, 
reason: HTTPS SSL error. You might want to force download through HTTP 
in the settings. 

###A:

Check file `~/.android/androidtool.cfg`

try to create it manually and add one line to it: 

```
sdkman.force.http=true 
```


Reference:https://groups.google.com/forum/#!topic/android-developers/Y6RBY6jQfNk



