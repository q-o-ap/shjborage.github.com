---
layout: post
title: "iOS7 初步适配，与iOS6共存"
date: 2013-10-14 15:43
comments: true
categories: iOS
---

#### 状态栏隐藏问题

**使用代码**
{% codeblock %}
- (BOOL)prefersStatusBarHidden
{
  return _isStatusHidden;//隐藏为YES，显示为NO
}

- (void)hideStatusBar:(BOOL)isHidden
{
  if ([self respondsToSelector:@selector(setNeedsStatusBarAppearanceUpdate)]) {
    // iOS 7
    _isStatusHidden = isHidden;
    [self performSelector:@selector(setNeedsStatusBarAppearanceUpdate)];
  } else {
    [[UIApplication sharedApplication] setStatusBarHidden:isHidden];
  }
}
{% endcodeblock %}

**Catagory** 此处与root ViewController有关，不一定这样解决 
{% codeblock %}
@implementation UINavigationController (StatusBar)

- (UIStatusBarStyle)preferredStatusBarStyle
{
  return UIStatusBarStyleLightContent;
}

- (BOOL)prefersStatusBarHidden
{
  if ([[self topViewController] isKindOfClass:[MovieDetailViewController class]]) {
    MovieDetailViewController *movieVC = (MovieDetailViewController *)[self topViewController];
    return [movieVC prefersStatusBarHidden];
  }

  return NO;
}

@end
{% endcodeblock %}

#### 与iOS6 共存问题

**旋转方面还是iOS6里面那些改动，iOS7基本上没啥变化**

主要区别就是相对坐标问题，简单处理如下：

定义全局父类，使得所有ViewController处理些事件。*每个都加显然很麻烦的啦*

```
//global_Header.h中相关的定义
#define kOSVersion (double)([[[UIDevice currentDevice] systemVersion] length] > 0 ? \
(double)[[[UIDevice currentDevice] systemVersion] doubleValue] : (double)kMinOSVersion)
```

{% codeblock %}

//
//  UniversalViewController.m
//  BFServiceStation
//
//  Created by Eric on 10/7/13.
//  Copyright (c) 2013 Baofeng. All rights reserved.
//

#import "UniversalViewController.h"
#import "global_Header.h"

@interface UniversalViewController ()

@end

@implementation UniversalViewController

- (id)initWithNibName:(NSString *)nibNameOrNil bundle:(NSBundle *)nibBundleOrNil
{
  self = [super initWithNibName:nibNameOrNil bundle:nibBundleOrNil];
  if (self) {
    // Custom initialization
  }
  return self;
}

- (void)viewDidLoad
{
  [super viewDidLoad];
	// Do any additional setup after loading the view.
  
  if (kOSVersion >= 7.0f) {
    self.automaticallyAdjustsScrollViewInsets = NO;
    [self setEdgesForExtendedLayout:UIRectEdgeNone];
  }
}

- (void)didReceiveMemoryWarning
{
  [super didReceiveMemoryWarning];
  // Dispose of any resources that can be recreated.
}

@end

{% endcodeblock %}

// 引用自CocoaChina

*	1、如果设置了self.navigationController.navigationBar 设置了UIBarMetricsLandscapePhone的背景;而没有设置UIBarMetricsDefault的背景，navigationBar会变透明

如图所示： navigationBar透明

{% img http://h.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=177bc31d0ef3d7ca08f63f73c224cf34/730e0cf3d7ca7bcb851f430cbc096b63f624a8a9.jpg?referer=3f277b9367380cd7bf0997dd0867&x=.jpg %}

 
*	2、上图还出现了一个问题，如果没有设置self.automaticallyAdjustsScrollViewInsets = NO，那么iOS7下会自动让scrollView滑动多次，以满足UITableView第一行的显示需要，这又带来了另外一个问题，下拉刷新的View的frame也随之发生了改变，导致整个下拉刷新不可用了
*	3、self.automaticallyAdjustsScrollViewInsets 这个属性是IOS7才有的新方法，目的就是为了让scrollView自动适应屏幕，如果self.automaticallyAdjustsScrollViewInsets = NO，那么效果会变成下图所示： 

{% img http://a.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=e257bde0d5ca7bcb797bc72a8e321a5e/0df3d7ca7bcb0a4662460f1a6963f6246b60afa9.jpg?referer=5ad9d42b0ed79123b9f7a1440967&x=.jpg %}

 
我的ViewController继承的是UITableViewController, 所以TableView是随系统而产生的，在IOS7之前，tableView的Frame的起始点是(0, 20),在IOS7之后，它的起始点变成了(0,0),这个时候如果为了让下拉刷新能正常工作，必须设置self.edgesForExtendedLayout = UIRectEdgeNone
（这种情况适用于容器建立在UINavigationController）.

*	4、如果设置了self.navigationController.navigationBar 设置UIBarMetricsDefault的背景为空，则默认的是IOS7上边栏平铺效果，如果设置了图片，并且这个图片高度保持在44（88）,那么IOS5,6,7的效果是一致的。
*	5、iOS7中的StatusBar的没有了背景，他的UIStatusBarStyleLightContent和UIStatusBarStyleDefault只会改变StatusBar的文字，都不会有背景
*	6、ViewController-Based Status Bar Appearance控制了StatusBar的外观，如果设置为YES，则会调用preferredStatusBarStyle。如果为NO,则通过传统方式来控制

	下图示IOS6，7上的正常情况

{% img http://f.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=d9ddefcc8fb1cb133a693c16ed6f2773/5fdf8db1cb134954e4566c47544e9258d1094a4d.jpg?referer=57d47c02708b47109738c8fcb643&x=.jpg %}

{% img http://b.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=9ec7af048882b90139adc33643b6d84a/1f178a82b9014a90a738c29eab773912b31bee8b.jpg?referer=40e418ff881363274cfaf7034a89&x=.jpg %}

{% img http://d.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=f3066050af4bd11300cdb7376a94d532/d043ad4bd11373f0b991ebbca60f4bfbfbed0462.jpg?referer=888f3612a38b87d609559e2f6421&x=.jpg %}


7、在IOS7下，如果不设置navigationBar的背景图片，而且设置[navigationBar setBarStyle:UIBarStyleBlackTranslucent];可以获取默认的黑色毛玻璃效果哟

如果大家还有遇到一些奇怪的问题，欢迎大家来补充

{% img http://a.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=f5b2e0c983cb39dbc5c06753e02d7819/50da81cb39dbb6fd7a1061050b24ab18972b374d.jpg?referer=aa507eef1c178a82972b4a907144&x=.jpg %}


本文参考文档:

http://blog.jaredsinclair.com/post/61507315630/wrestling-with-status-bars-and-navigation-bars-on-ios-7
https://developer.apple.com/library/ios/documentation/userexperience/conceptual/TransitionGuide/index.html
