---
layout: post
title: "解决iOS报错libc++abi.dylib handler threw exception"
date: 2014-01-03 23:57
comments: true
categories: iOS
---

这个错有时很奇怪，有时比较正常的情况也会报这个，可能与XCode版本有关。
下文列举一些情况吧

<!-- more -->

###可能引起的情况

*	如果是多语言的话，要查看xib是否支持改语言～就是要在xib里面的Localiztion选项里面检查。（至少xcode4.5里面是这样的，或许对someone 有帮助）
*	因为子类需要横屏，父类没设置横屏，所以爆出异常来。Supported orientations has no common orientation with the application, and shouldAutorotate is returning YES  
*	@｛｝ 语法糖，数据出错后也会报这个错
*	数据越界


###定位异常的方法

```
@try {  
}  
@catch(NSException *exception) {  
  NSLog(@"exception:%@", exception);  
}
@finally {            
}  
```


