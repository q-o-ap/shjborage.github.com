---
layout: post
title: "性能提升-是否缓存图片-imageNamed与imageWithContentsOfFile的区别"
date: 2014-03-15 01:01
comments: true
categories: iOS
---

常见的从bundle中加载图片的方式有两种，一个是用`imageNamed`，二是用`imageWithContentsOfFile`，第一种比较常见一点。

既然有两种类似的方法来实现相同的目的，那么他们之间的差别是什么呢？

`imageNamed`的优点是当加载时会缓存图片。`imageNamed`的文档中这么说:
这个方法用一个指定的名字在系统缓存中查找并返回一个图片对象如果它存在的话。如果缓存中没有找到相应的图片，这个方法从指定的文档中加载然后缓存并返回这个对象。

相反的，`imageWithContentsOfFile`仅加载图片。

下面的代码说明了这两种方法的用法:

```
UIImage *img = [UIImage imageNamed:@"myImage"];              // caching
// or
UIImage *img = [UIImage imageWithContentsOfFile:@"myImage"]; // no caching
```
那么我们应该如何选择呢？

如果你要加载一个大图片而且是一次性使用，那么就没必要缓存这个图片，用`imageWithContentsOfFile`足矣，这样不会浪费内存来缓存它。

然而，在图片反复重用的情况下`imageNamed`是一个好得多的选择。

更多阅读：[iOS应用性能调优的25个建议和技巧](http://blog.jobbole.com/37984/)