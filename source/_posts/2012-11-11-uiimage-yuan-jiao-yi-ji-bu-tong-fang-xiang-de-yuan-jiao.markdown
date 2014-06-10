---
layout: post
title: "UIImage 圆角以及不同方向的圆角"
date: 2012-11-11 12:43
comments: true
categories: iOS
tags: iOS UI
---

自己对这个需求进行了重新封装，方便大家使用。

```
//
//  UIImage+RoundRect.h
//
//  Created by Eric on 11/11/12.
//  Copyright (c) 2012 Saick. All rights reserved.
//
 
#import <UIKit/UIKit.h>
 
typedefenum {
    UIImageRoundedCornerTopLeft = 1,
    UIImageRoundedCornerTopRight = 1 << 1,
    UIImageRoundedCornerBottomRight = 1 << 2,
    UIImageRoundedCornerBottomLeft = 1 << 3
} UIImageRoundedCorner;
 
@interface UIImage (RoundRect)
 
- (UIImage *)roundedRectWith:(float)radius;
- (UIImage *)roundedRectWith:(float)radius cornerMask:(UIImageRoundedCorner)cornerMask;
 
@end
```
 

```
//
//  UIImage+RoundRect.m
//  iSocial
//
//  Created by Eric on 11/11/12.
//  Copyright (c) 2012 Saick. All rights reserved.
//

#import "UIImage+RoundRect.h"

@implementation UIImage (RoundRect)

//UIKit坐标系统原点在左上角，y方向向下的（坐标系A），但在Quartz中坐标系原点在左下角，y方向向上的(坐标系B)。图片绘制也是颠倒的。
static void addRoundedRectToPath(CGContextRef context, CGRect rect, float radius, UIImageRoundedCorner cornerMask)
{
  //原点在左下方，y方向向上。移动到线条2的起点。
  CGContextMoveToPoint(context, rect.origin.x, rect.origin.y + radius);
  
  //画出线条2, 目前画线的起始点已经移动到线条2的结束地方了。
  CGContextAddLineToPoint(context, rect.origin.x, rect.origin.y + rect.size.height - radius);
  
  //如果左上角需要画圆角，画出一个弧线出来。
  if (cornerMask & UIImageRoundedCornerTopLeft) {
    //已左上的正方形的右下脚为圆心，半径为radius， 180度到90度画一个弧线，
    CGContextAddArc(context, rect.origin.x + radius, rect.origin.y + rect.size.height - radius,
                    radius, M_PI, M_PI / 2, 1);
  } else {
    //如果不需要画左上角的弧度。从线2终点，画到线3的终点，
    CGContextAddLineToPoint(context, rect.origin.x, rect.origin.y + rect.size.height);
    
    //线3终点，画到线4的起点
    CGContextAddLineToPoint(context, rect.origin.x + radius, rect.origin.y + rect.size.height);
  }
  
  //画线4的起始，到线4的终点
  CGContextAddLineToPoint(context, rect.origin.x + rect.size.width - radius,
                          rect.origin.y + rect.size.height);
  
  //画右上角
  if (cornerMask & UIImageRoundedCornerTopRight) {
    CGContextAddArc(context, rect.origin.x + rect.size.width - radius,
                    rect.origin.y + rect.size.height - radius, radius, M_PI / 2, 0.0f, 1);
  } else {
    CGContextAddLineToPoint(context, rect.origin.x + rect.size.width, rect.origin.y + rect.size.height);
    CGContextAddLineToPoint(context, rect.origin.x + rect.size.width, rect.origin.y + rect.size.height - radius);
  }
  
  CGContextAddLineToPoint(context, rect.origin.x + rect.size.width, rect.origin.y + radius);
  
  //画右下角弧线
  if (cornerMask & UIImageRoundedCornerBottomRight) {
    CGContextAddArc(context, rect.origin.x + rect.size.width - radius, rect.origin.y + radius,
                    radius, 0.0f, -M_PI / 2, 1);
  } else {
    CGContextAddLineToPoint(context, rect.origin.x + rect.size.width, rect.origin.y);
    CGContextAddLineToPoint(context, rect.origin.x + rect.size.width - radius, rect.origin.y);
  }
  
  CGContextAddLineToPoint(context, rect.origin.x + radius, rect.origin.y);
  
  //画左下角弧线
  if (cornerMask & UIImageRoundedCornerBottomLeft) {
    CGContextAddArc(context, rect.origin.x + radius, rect.origin.y + radius, radius,
                    -M_PI / 2, M_PI, 1);
  } else {
    CGContextAddLineToPoint(context, rect.origin.x, rect.origin.y);
    CGContextAddLineToPoint(context, rect.origin.x, rect.origin.y + radius);
  }
  
  CGContextClosePath(context);
}

- (UIImage *)roundedRectWith:(float)radius
{
  return [self roundedRectWith:radius cornerMask:UIImageRoundedCornerBottomLeft | UIImageRoundedCornerBottomRight | UIImageRoundedCornerTopLeft | UIImageRoundedCornerTopRight];
}

- (UIImage *)roundedRectWith:(float)radius cornerMask:(UIImageRoundedCorner)cornerMask
{
  UIImageView *bkImageViewTmp = [[[UIImageView alloc] initWithImage:self] autorelease];
  
  int w = self.size.width;
  int h = self.size.height;
  
  CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceRGB();
  CGContextRef context = CGBitmapContextCreate(NULL, w, h, 8, 4 * w, colorSpace, kCGImageAlphaPremultipliedFirst);
  
  CGContextBeginPath(context);
  addRoundedRectToPath(context,bkImageViewTmp.frame, radius, cornerMask);
  CGContextClosePath(context);
  CGContextClip(context);
  
  CGContextDrawImage(context, CGRectMake(0, 0, w, h), self.CGImage);
  
  CGImageRef imageMasked = CGBitmapContextCreateImage(context);
  CGContextRelease(context);
  CGColorSpaceRelease(colorSpace);
  
  UIImage *newImage = [UIImage imageWithCGImage:imageMasked];
  
  CGImageRelease(imageMasked);
  
  return newImage;
}

@end
```
 
转自http://www.cnblogs.com/likwo/archive/2011/06/16/2082781.html
 
一般图片圆角显示都是四个角都显示圆角，如果只有这个功能需求，采用常用的方法就可以了，也不用费那么多事情。但是在有些情况下需要只显示图片的2个圆角，就不太好实现了。

先看效果图 ，未做圆角转换的图片 

<iframe src="https://www.flickr.com/photos/shjborage/13381665273/player/ef331c3000" height="487" width="328"  frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

做了4个角的圆角转换的图片

<iframe src="https://www.flickr.com/photos/shjborage/13381664873/player/0283605b27" height="460" width="322"  frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

只做了下面2个圆角转换的图片

<iframe src="https://www.flickr.com/photos/shjborage/13381874274/player/0060efcde0" height="460" width="324"  frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>
