---
layout: post
title: "iOS 开发常见低级错误"
date: 2013-05-28 10:19
comments: true
categories: iOS
tags: iOS Obj-C
---

***不断更新中。。。***

Dec 2, 2013

####使用.mm文件编译成.a静态库，或放入CocoaPods中，报错

```
operator delete(void*)", referenced from xxx
```
解决方案：引入libc++.dylib

[Origin](http://stackoverflow.com/questions/16865260/quickblox-linker-error-operator-deletevoid-referenced-from)

Aug 5, 2013

####如果使用C++代码混编，找不到iostream等
	可能是.m中直接import的，这个需要注意改成mm或者修改编译方式


May 28, 2013

####不要调用[super  release] 
	在dealloc里要调用[super dealloc]，千万不要调用[super release]
	
	
	
