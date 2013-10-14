---
layout: post
title: "自定义UITabbarController引发的血案"
date: 2013-09-02 09:05
comments: true
categories: iOS
---

转自：http://www.cnblogs.com/chen1987lei/archive/2012/05/19/2509057.html


nested push animation can result in corrupted navigation bar 嵌套的navigation动画会造成 navigation bar 错误， 具体表现为上一层的titleview 和这一层重叠， navigationbaritem 维持上一层的左按钮， 右按钮 、、、系统警告提示nested push animation can result in corrupted navigation bar ， 大家遇到过这种情况吗？？ 
嵌套的动画， 是什么意思呢， 为什么会这样， 要怎么解决


结贴~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


有一天，从头到尾分析了整个项目的代码， 最后确认问题根源——自定义UITabbarController引发的血案
项目中使用了自定义的UITabbarController ，修改里面的tabbar视图， 在这个UITabbarController 中实现了viewWillAppear方法， 里面没有加super viewWillAppear ， 结果导致了这一连串的问题，
整个软件里面，都会出现弹一次模态窗口，导航视图就会动画乱序，
导航视图前进后退过快， 导致导航栏嵌套， 一个上面覆盖另一个。


扩展阅读：
http://stackoverflow.com/questions/5525519/iphone-uinavigation-issue-nested-push-animation-can-result-in-corrupted-naviga

其实主要还是在用户看不到的时候，多次使用Animate=YES