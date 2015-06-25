---
layout: post
title: "iOS调试方法定位Crash以及静态库调试"
date: 2015-02-07 23:56
comments: true
categories: iOS
tags: iOS 调试
---

## 引子
在iOS开发中常会遇到Crash，有时Xcode并不能很好的提示我们出错的原因是什么，具体是哪行代码出了问题。<br>
比如 [UNRECOGNIZED SELECTOR SENT TO INSTANCE 问题快速定位的方法](http://blog.objcc.com/unrecognized-selector-sent-to-instance/) 文中提到的定位处理不了的消息发送。

## Symbolic Breakpoint
在调试时，我们经常需要使用到断点，Xcode支持几种断点：

+   普通断点
+   Exception Breakpoint
+   Symbolic Breakpoint
+   。。。

我们可能比较熟悉普通断点，主要来介绍一下 `Exception Breakpoint` 和 `Symbolic Breakpoint`.

***Exception Breakpoint***<br>
全局异常断点，傻瓜式的使用，在任意的代码处有异常抛出没有捕获即可“被断住”。<br>
*注：如果使用了C、C++开发的话，有些signal可能会被这种断点触发，但并不会引发Crash*

***Symbolic Breakpoint***<br>
通过类名以及方法名来添加断点的一种方式。例如：
```
-[NSObject(NSObject) doesNotRecognizeSelector:]
```

### 包含静态库的整体调试
如果想测试在某个静态库中某个方法是否执行，可以在知道类名跟方法名的情况下使用 `Symbolic Breakpoint` 来确认这个情况
####送个命令～
可以在某个静态库或可执行文件中查找 symbol。 需要详细的使用方法，请自行`man nm`
```
// nm - display name list (symbol table)
nm xxx.a | grep xxSymbol
```