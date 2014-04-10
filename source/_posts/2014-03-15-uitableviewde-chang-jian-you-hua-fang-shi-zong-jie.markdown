---
layout: post
title: "UITableView的常见优化方式总结"
date: 2014-03-15 00:07
comments: true
categories: iOS
---

众所周知，我们在iOS开发过程中与面试中，常见的问题就包括UITableView的滚动性能，足以见得这个功能的重要性。
为了保证table view平滑滚动，确保你采取了以下的措施:

*	正确使用`reuseIdentifier`来重用cells
*	尽量使所有的view opaque，包括cell自身
*	避免渐变，图片缩放，后台选人
*	缓存行高
*	如果cell内现实的内容来自web，使用异步加载，缓存请求结果
*	使用`shadowPath`来画阴影
*	减少subviews的数量
*	尽量不适用`cellForRowAtIndexPath:`，如果你需要用到它，只用一次然后缓存结果
*	使用正确的数据结构来存储数据
*	使用`rowHeight`, `sectionFooterHeight` 和 `sectionHeaderHeight`来设定固定的高，不要请求delegate


更多阅读：[iOS应用性能调优的25个建议和技巧](http://blog.jobbole.com/37984/)

