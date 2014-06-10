---
layout: post
title: "NaN问题 Not a Number"
date: 2013-10-10 23:26
comments: true
categories: iOS
tags: iOS Crash
---

这种错误是在float经过函数运行出了不是数字的值，nan的意思就是not a number。  

主要常见原因：

*	除以0
*	sizeWithFont的字符串为nil
*	数学函数不正确运算

解决方法除了排除根源所在之外，用函数isnan()也是不错的选择（至少在没有彻底解决以前）
如下

```
float _x = NAN;
if (!isnan(_x)) {
cell.imgView.frame = CGRectMake(_x, 8, 10, 12);
}
```


引自：http://www.minroad.com/?p=403 