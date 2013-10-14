---
layout: post
title: "在应用中从竖屏模式强制转换为横屏模式 & 状态栏白边问题"
date: 2013-10-14 16:28
comments: true
categories: iOS
---

####序：状态栏白边问题解决
在旋转前，先隐藏掉状态栏，再设置相关的Frame。（别犯低级错误即可）



在 iPhone 应用里，有时我们想强行把显示模式从纵屏改为横屏（反之亦然），CocoaChina 会员 “alienblue” 为我们提供了两种思路

###第一种：通过人为的办法改变view.transform的属性。
具体办法：
    
`view.transform`一般是View的旋转，拉伸移动等属性，类似`view.layer.transform`，区别在于`view.transform`是二维的，也就是使用仿射的办法通常就是带有前缀CGAffineTransform的类（可以到API文档里面搜索这个前缀的所有类），而`view.layer.transform`可以在3D模式下面的变化，通常使用的都是前缀为CATransform3D的类。
    
   这里要记住一点，当你改变过一个`view.transform`属性或者`view.layer.transform`的时候需要恢复默认状态的话，记得先把他们重置可以使用`view.transform = CGAffineTransformIdentity`，或者`view.layer.transform = CATransform3DIdentity`，假设你一直不断的改变一个`view.transform`的属性，而每次改变之前没有重置的话，你会发现后来的改变和你想要的发生变化了，不是你真正想要的结果。
    
   好了，上面介绍了旋转的属性，接下来就是关键了。官方提供了一个办法就是查看当前电池条的状态`UIInterfaceOrientation orientation = [UIApplication sharedApplication].statusBarOrientation;`通过这个办法，你可以知道当前屏幕的电池条的显示方向，而且你还可以强制设置他的显示方向，通过设置这个属性就OK了，可以选择是否动画改变电池条方向。有了这两个那我们就可以任意的改变我们想要的显示方式了。
    
*	1.获取当前电池条的方向

```
UIInterfaceOrientation orientation = [UIApplication sharedApplication].statusBarOrientation
```

*	2.获取当前屏幕的大小

```
CGRect frame = [UIScreen mainScreen].applicationFrame;
```

*	3.设置我们的View的中心点

```
    CGPoint center = CGPointMake(frame.origin.x + ceil(frame.size.width/2), frame.origin.y + ceil(frame.size.height/2));
```

*	4.根据当前电池条的方向，获取需要旋转的角度的大小。通常

```
	if (orientation == UIInterfaceOrientationLandscapeLeft) {
        return CGAffineTransformMakeRotation(M_PI*1.5);
    } else if (orientation == UIInterfaceOrientationLandscapeRight) {
        return CGAffineTransformMakeRotation(M_PI/2);
    } else if (orientation == UIInterfaceOrientationPortraitUpsideDown) {
        return CGAffineTransformMakeRotation(-M_PI);
    } else {
        return CGAffineTransformIdentity;
    }
```

*	5.可以动画的改变我们view的显示方式了

{% codeblock %}
[[UIApplication sharedApplication] setStatusBarOrientation:UIDeviceOrientationLandscapeRight animated:YES];

CGFloat duration = [UIApplication sharedApplication].statusBarOrientationAnimationDuration;（获取当前电池条动画改变的时间）
[UIView beginAnimations:nil context:nil];
[UIView setAnimationDuration:duration];

//在这里设置view.transform需要匹配的旋转角度的大小就可以了。
[UIView commitAnimations];
        
{% endcodeblock %}

###第二种：通过setOrientation:的办法强制性的旋转到一个特定的方向。
   
   注意：Apple在3.0以后都不支持这个办法了，这个办法已经成为了私有的了，但是要跳过App Stroe的审核，需要一点巧妙的办法。
    
   不要直接调用`[[UIDevice currentDevice] setOrientation: UIInterfaceOrientationLandscapeRight]`这样的办法来强制性的横屏，这样导致你的程序是很难通过App Store审核的。但是你可以选择使用performSelector的办法来调用它。具体就几行代码如下：
   
    
{% codeblock %}
//强制横屏
    if ([[UIDevice currentDevice] respondsToSelector:@selector(setOrientation:)]) {
        [[UIDevice currentDevice] performSelector:@selector(setOrientation:)
                                       withObject:(id)UIInterfaceOrientationLandscapeRight];
    }
    
    // 更保险的写法 add by Eric
    + (void)changeOrientation:(UIInterfaceOrientation)toOrientation
    {
  	if ([[UIDevice currentDevice] respondsToSelector:NSSelectorFromString(@"setOrientation:")]) {
    	[[UIDevice currentDevice] performSelector:NSSelectorFromString(@"setOrientation:")
                                   withObject:(id)toOrientation];
  	}
}
    
{% endcodeblock %} 

   
   总结:如果第一种办法可以满足你需要的话，最好使用第一种办法，因为那个上 App Store肯定没问问题，但是第二种的话是需要冒风险的，但是如果你的结构太复杂了，导致使用第一种办法人为很难控制的话，可以尝试简单的使用第二种办法。