---
layout: post
title: "iOS ARC 兼容处理"
date: 2013-10-19 01:56
comments: true
categories: iOS
---

From:宋红日（转自：http://blog.sina.com.cn/s/blog_9792a85b01018v83.html）

我们经常在引用第三方控件或者阅读一个开源的代码的时候，与自己的xcode 的开发环境不一样，导致编译不过的问题，还得改代码，带来了一定的工作量。其实有一下方法做到兼容处理。统一用下面定义的宏就可以。
尤其当你决定做一个通用的控件 或 模块的时候。

```
// ARC conditions
#if !__has_feature(objc_arc)
#define Release(obj) [obj release]
#define Retain(obj) [obj retain]
#else 
#define Release(obj)
#define Retain(obj) obj
#endif
```



另外，如果是ARC工程，某些文件不支持ARC的话，可以在Target->Build Phases->Complie sources 中对某个文件加入Complier Flags “-fno-objc-arc”

如下图：  

{% img http://c.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=21b1372bfadcd100c99cf82442b0362d/9e3df8dcd100baa1d767da9d4510b912c8fc2e48.jpg?referer=58d17125ca95d1438361d1138a46&x=.jpg %}
