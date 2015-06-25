---
layout: post
title: "iOS 获取当前正在显示的ViewController"
date: 2015-01-05 23:07
comments: true
categories: iOS
tags: iOS 坑 纯技术
---

###引子

在做 iOS 开发时，我们经常在一个 ViewController 中 presentModelViewController 另外一个 ViewController。看似简单，但在与 UIActionSheet 和 UIAlertView 结合起来使用时，很容易出现问题。问题如下： <http://stackoverflow.com/questions/21158939/warning-attempt-to-present-uimodalitemspresentingviewcontroller-on-uimodalitema>

```
"Warning: Attempt to present `<_UIModalItemsPresentingViewController:>  on 
<_UIModalItemAppViewController:> which is already presenting 
<_UIModalItemsPresentingViewController:>"`
```

类似的问题是因为 UIAlertView 或 UIActionSheet 在系统级使用了 presentModelViewController，并且修改了 Application 中更换了 keyWindow，在 keyWindow 的 rootViewController 中 presentModelViewController，如果当前 present 还未完成时，再 present 别的界面，就会报这个错。

###解决方案
在 present 时，不直接使用 keyWindow，使用如下代码：

```
//获取当前屏幕显示的viewcontroller
- (UIViewController *)getCurrentVC
{
    UIViewController *result = nil;
    
    UIWindow * window = [[UIApplication sharedApplication] keyWindow];
    if (window.windowLevel != UIWindowLevelNormal)
    {
        NSArray *windows = [[UIApplication sharedApplication] windows];
        for(UIWindow * tmpWin in windows)
        {
            if (tmpWin.windowLevel == UIWindowLevelNormal)
            {
                window = tmpWin;
                break;
            }
        }
    }
    
    UIView *frontView = [[window subviews] objectAtIndex:0];
    id nextResponder = [frontView nextResponder];
    
    if ([nextResponder isKindOfClass:[UIViewController class]])
        result = nextResponder;
    else
        result = window.rootViewController;
    
    return result;
}
```


查看更多：<http://blog.csdn.net/worldzhy/article/details/42120929>