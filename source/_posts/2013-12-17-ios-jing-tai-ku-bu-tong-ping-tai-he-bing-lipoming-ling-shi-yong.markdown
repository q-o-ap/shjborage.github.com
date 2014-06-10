---
layout: post
title: "iOS 静态库不同平台合并 lipo命令使用"
date: 2013-12-17 23:54
comments: true
categories: iOS
tags: iOS Obj-C 底层 第三方库
---

###Introduction

查看*lipo*详解

```
man lipo
```

**lipo - create or operate on universal files**

<!-- more -->

###Usage

####Simple

**lipo -create xxx_arm.a xxx_i386.a -output xxx_universal.a**

Example:

```
lipo -create /Users/pjk1129/Library/Developer/Xcode/DerivedData/Print-dgfkluumuexoxhcapzidtsmdgqcj/Build/Products/Release-iphonesimulator/libPrint.a  /Users/pjk1129/Library/Developer/Xcode/DerivedData/Print-dgfkluumuexoxhcapzidtsmdgqcj/Build/Products/Release-iphoneos/libPrint.a  -output /Users/pjk1129/Desktop/libPrint.a
```

####Shell

*libevent.sh*
```
#! /bin/sh
mkdir universal
lipo -create simulator/libevent.a device/libevent.a -o universal/libevent.a
lipo -create simulator/libevent_extra.a device/libevent_extra.a -o universal/libevent_extra.a
lipo -create simulator/libevent_core.a device/libevent_core.a -o universal/libevent_core.a
lipo -create simulator/libevent_pthreads.a device/libevent_pthreads.a -o universal/libevent_pthreads.a
```

###Review Info

**lipo -info xxx_universal.a**

Example:

```
lipo -info /Users/pjk1129/Desktop/libPrint.a 
```