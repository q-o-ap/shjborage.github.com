---
layout: post
title: "iOS7 UI兼容 导航栏按钮边框 UINavigationItem left and right padding"
date: 2014-01-20 23:16
comments: true
categories: iOS
---

iOS7之前的UI为：

<iframe src="https://www.flickr.com/photos/shjborage/12052252814/player/120d793b08" height="44" width="324"  frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

而在iOS7中，由于设计方面的原因，使得UI变为:

<iframe src="https://www.flickr.com/photos/shjborage/12052149003/player/bb36320bc9" height="44" width="322"  frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

 
<!-- more -->

修改的方法重写UINavigationItem的setLeftBarButtonItem和setRightBarButtonItem方法，使之与之前版本兼容；

代码如下：

*UINavigationItem+margin.h*

```
//
//  UINavigationItem+margin.h
//
//  Created by shjborage on 1/20/14.
//  Copyright (c) 2014 Saick. All rights reserved.
//

#import <UIKit/UIKit.h>

@interface UINavigationItem (margin)

@end

```

*UINavigationItem+margin.m*

```
//
//  UINavigationItem+margin.m
//
//  Created by shjborage on 1/20/14.
//  Copyright (c) 2014 Saick. All rights reserved.
//

#import "UINavigationItem+margin.h"

@implementation UINavigationItem (margin)

#if __IPHONE_OS_VERSION_MAX_ALLOWED > __IPHONE_6_1

- (void)setLeftBarButtonItem:(UIBarButtonItem *)_leftBarButtonItem
{
  if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 7.0) {
    UIBarButtonItem *negativeSeperator = [[UIBarButtonItem alloc] initWithBarButtonSystemItem:UIBarButtonSystemItemFixedSpace target:nil action:nil];
    negativeSeperator.width = -10.0f;
    
    if (_leftBarButtonItem) {
      [self setLeftBarButtonItems:@[negativeSeperator, _leftBarButtonItem]];
    } else {
      [self setLeftBarButtonItems:@[negativeSeperator]];
    }
  } else {
    [self setLeftBarButtonItem:_leftBarButtonItem animated:NO];
  }
}

//- (void)setLeftBarButtonItems:(NSArray *)leftBarButtonItems
//{
//  if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 7.0) {
//    UIBarButtonItem *negativeSeperator = [[UIBarButtonItem alloc] initWithBarButtonSystemItem:UIBarButtonSystemItemFixedSpace target:nil action:nil];
//    negativeSeperator.width = -10.0f;
//    
//    if ([_leftBarButtonItems count] > 0) {
//      NSMutableArray *items = [NSMutableArray arrayWithArray:leftBarButtonItems];
//      [items insertObject:negativeSeperator atIndex:0];
//      [self setLeftBarButtonItems:items animated:NO];
//    } else {
//      [self setLeftBarButtonItems:leftBarButtonItems animated:NO];
//    }
//  } else {
//    [self setLeftBarButtonItems:leftBarButtonItems animated:NO];
//  }
//}

- (void)setRightBarButtonItem:(UIBarButtonItem *)_rightBarButtonItem
{
  if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 7.0) {
    UIBarButtonItem *negativeSeperator = [[UIBarButtonItem alloc] initWithBarButtonSystemItem:UIBarButtonSystemItemFixedSpace target:nil action:nil];
    negativeSeperator.width = -10.0f;
    
    if (_rightBarButtonItem) {
      [self setRightBarButtonItems:@[negativeSeperator, _rightBarButtonItem]];
    } else {
      [self setRightBarButtonItems:@[negativeSeperator]];
    }
  } else {
    [self setRightBarButtonItem:_rightBarButtonItem animated:NO];
  }
}

//- (void)setRightBarButtonItems:(NSArray *)rightBarButtonItems
//{
//  if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 7.0) {
//    UIBarButtonItem *negativeSeperator = [[UIBarButtonItem alloc] initWithBarButtonSystemItem:UIBarButtonSystemItemFixedSpace target:nil action:nil];
//    negativeSeperator.width = -10.0f;
//    
//    if ([_leftBarButtonItems count] > 0) {
//      NSMutableArray *items = [NSMutableArray arrayWithArray:rightBarButtonItems];
//      [items insertObject:negativeSeperator atIndex:0];
//      [self setRightBarButtonItems:items animated:NO];
//    } else {
//      [self setRightBarButtonItems:rightBarButtonItems animated:NO];
//    }
//  } else {
//    [self setRightBarButtonItems:rightBarButtonItems animated:NO];
//  }
//}

#endif

@end

```


项目源码不做任何修改，问题解决；

添加Button：

```
UIBarButtonItem *leftBarItem = [[UIBarButtonItem alloc] initWithCustomView:btnBack];
self.navigationItem.leftBarButtonItem = leftBarItem;
[leftBarItem release];
```

如果原来项目中有使用`self.navigationItem.leftBarButtonItems`或`self.navigationItem.rightBarButtonItems`可参考注释部分内容。如果都是多个的，那么直接换把注释解开，把单个的给注释掉。**搞懂原理才是重要的**
 
[原文地址](http://www.cnblogs.com/maxfong/p/3375167.html)