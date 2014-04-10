---
layout: post
title: "iOS Crash:nested pop animation can result in corrupted navigation bar nested pop animation can result in .."
date: 2013-12-01 01:37
comments: true
categories: iOS
---

[Origin](http://yul100887.blog.163.com/blog/static/20033613520137205121620/)

Nested pop animation can result in corrupted navigation bar

Finishing up a navigation transition in an unexpected state. Navigation Bar subview tree might get corrupted.


###【情况一】

出现此问题的原因是在A界面弹出B界面完成后，又掉A界面POP出栈，这必然导致以上问题。如下，在弹出personalController后面，又在后面调用了[self.navigationController popViewControllerAnimated:YES];将自己弹出！解决方法，还用说吗？｛删除最后一行代码，要弹出此界面，自然有自己的返回按钮来处理｝，至少在B界面未POP以前，A界面是无论如何也不能将自己POP去消毁！

<!-- more -->

```
NSString *memberId = [dictionary objectForKey:@"PlayerNo"];
PersonalInfoViewController* personalController = [[PersonalInfoViewController alloc] init];
[personalController.navigationItems etHidesBackButton:YES];
[personalController setMemberId:memberId];
[self.navigationController pushViewController:personalController animated:YES];
[personalController release];
[self.navigationController popViewControllerAnimated:YES];
```

###【情况二】


nested push animation can result in corrupted navigation bar   
嵌套的navigation动画会造成  navigation bar 错误  
具体表现为上一层的titleview 和这一层重叠， navigationbaritem维持上一层的左按钮,右按钮,系统警告提示nested push animation can result in corrupted navigation bar

从头到尾分析了整个项目的代码， 最后确认问题根源——**自定义UITabbarController**引发的血案
项目中使用了自定义的UITabbarController ，修改里面的tabbar视图， 在这个UITabbarController 中实现了viewWillAppear方法， 里面没有加super viewWillAppear ， 结果导致了这一连串的问题，

*	整个软件里面，都会出现弹一次模态窗口，导航视图就会动画乱序，
*	导航视图前进后退过快， 导致导航栏嵌套， 一个上面覆盖另一个。

```
- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];
}
```