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

<!-- more -->

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

<a href="http://www.flickr.com/photos/105999540@N03/10374769665/" title="2013-10-14-4 by EricShj, on Flickr"><img src="http://farm6.staticflickr.com/5503/10374769665_9d71dd11b1.jpg" width="331" height="135" alt="2013-10-14-4"></a>

 
*	2、上图还出现了一个问题，如果没有设置self.automaticallyAdjustsScrollViewInsets = NO，那么iOS7下会自动让scrollView滑动多次，以满足UITableView第一行的显示需要，这又带来了另外一个问题，下拉刷新的View的frame也随之发生了改变，导致整个下拉刷新不可用了
*	3、self.automaticallyAdjustsScrollViewInsets 这个属性是IOS7才有的新方法，目的就是为了让scrollView自动适应屏幕，如果self.automaticallyAdjustsScrollViewInsets = NO，那么效果会变成下图所示： 

<a href="http://www.flickr.com/photos/105999540@N03/10374769645/" title="2013-10-14-5 by EricShj, on Flickr"><img src="http://farm3.staticflickr.com/2856/10374769645_03d52ff8e7.jpg" width="332" height="122" alt="2013-10-14-5"></a>

 
我的ViewController继承的是UITableViewController, 所以TableView是随系统而产生的，在IOS7之前，tableView的Frame的起始点是(0, 20),在IOS7之后，它的起始点变成了(0,0),这个时候如果为了让下拉刷新能正常工作，必须设置self.edgesForExtendedLayout = UIRectEdgeNone
（这种情况适用于容器建立在UINavigationController）.

*	4、如果设置了self.navigationController.navigationBar 设置UIBarMetricsDefault的背景为空，则默认的是IOS7上边栏平铺效果，如果设置了图片，并且这个图片高度保持在44（88）,那么IOS5,6,7的效果是一致的。
*	5、iOS7中的StatusBar的没有了背景，他的UIStatusBarStyleLightContent和UIStatusBarStyleDefault只会改变StatusBar的文字，都不会有背景
*	6、ViewController-Based Status Bar Appearance控制了StatusBar的外观，如果设置为YES，则会调用preferredStatusBarStyle。如果为NO,则通过传统方式来控制

	下图示IOS6，7上的正常情况

<a href="http://www.flickr.com/photos/105999540@N03/10374785016/" title="2013-10-14-6 by EricShj, on Flickr"><img src="http://farm3.staticflickr.com/2859/10374785016_dd23dccba9.jpg" width="332" height="122" alt="2013-10-14-6"></a>

<a href="http://www.flickr.com/photos/105999540@N03/10374784856/" title="2013-10-14-7 by EricShj, on Flickr"><img src="http://farm6.staticflickr.com/5501/10374784856_3cfc6e2085.jpg" width="322" height="210" alt="2013-10-14-7"></a>

<a href="http://www.flickr.com/photos/105999540@N03/10374769455/" title="2013-10-14-8 by EricShj, on Flickr"><img src="http://farm3.staticflickr.com/2840/10374769455_0e5a15cd77.jpg" width="394" height="155" alt="2013-10-14-8"></a>


7、在IOS7下，如果不设置navigationBar的背景图片，而且设置[navigationBar setBarStyle:UIBarStyleBlackTranslucent];可以获取默认的黑色毛玻璃效果哟

如果大家还有遇到一些奇怪的问题，欢迎大家来补充

<a href="http://www.flickr.com/photos/105999540@N03/10374769885/" title="2013-10-14-9 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7389/10374769885_12ea31ec14_z.jpg" width="640" height="363" alt="2013-10-14-9"></a>


本文参考文档:

http://blog.jaredsinclair.com/post/61507315630/wrestling-with-status-bars-and-navigation-bars-on-ios-7
https://developer.apple.com/library/ios/documentation/userexperience/conceptual/TransitionGuide/index.html
