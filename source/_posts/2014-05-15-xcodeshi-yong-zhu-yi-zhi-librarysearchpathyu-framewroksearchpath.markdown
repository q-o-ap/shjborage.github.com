---
layout: post
title: "XCode使用注意之-LibrarySearchPath与FramewrokSearchPath"
date: 2014-05-15 23:03
comments: true
categories: iOS
tags : iOS XCode ShareSDK
---

在iOS的工程中，尤其是在使用[CocoaPods](http://cocoapods.org/)前，经常会把某个静态库或者Framework直接拖到工程中，编译运行通过，这样就可以了么？

这里面有几个需要注意的问题，希望引起自己与同仁们的注意：

*	`.a`默认不会被svn管理起来，git有待测试（没在公司用过这东西），需要特别注意这个事情
*	`library search path` 与 `framework search path`，默认都是以本地的绝对路径添加进来的，需要改成相对路径。这个 `library search path` 可能用的比较多，比较熟悉，`framework search path` 也是一样的道理，使用过了次 [ShareSDK](http://sharesdk.cn) 就被这个问题卡了一小下，特别把这个事件给写下来。