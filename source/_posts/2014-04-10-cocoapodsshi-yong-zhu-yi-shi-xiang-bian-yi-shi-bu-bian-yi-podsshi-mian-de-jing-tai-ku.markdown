---
layout: post
title: "CocoaPods使用注意事项-编译时不编译Pods时面的静态库"
date: 2014-04-10 21:59
comments: true
categories: iOS 
tags: iOS CocoaPods
---

自从决定开始用CocoaPods以后，肯定就是根本停不下来的节奏。但是由于CocoaPods本身也是处于不断的开发与改进中的，自然也免不了各种问题。当然了，如果懂Ruby可以为社区贡献一份力量，如果不懂的话呢，就只能先总结总结，给自己减少一些犯错的“机会”吧。

<!-- more -->

*进入正题*  
升级Xcode5.1后，默认必须支持arm64，这个对于很多项目来说有一些问题，尤其是对于一些第三方库，比如`友盟统计`、`ShareSDK`等。 这些库很多都没有做好为64bit升级的准备，所以很多时候我们还不能支持64bit。
因为本文不是讲64bit的支持，所以这里就不多说这个了，教程与使用方法应该也有很多。   
这里列举一种在`Podfile`中让CocoaPods指定编译方式的方法

```
# Remove 64-bit build architecture from Pods targets
post_install do |installer|
  installer.project.targets.each do |target|
    target.build_configurations.each do |configuration|
      target.build_settings(configuration.name)['ARCHS'] = '$(ARCHS_STANDARD_32_BIT)'
    end
  end
end
```
####切入

下图中的配置一定要与`Pods`工程中一致否则就会导致编译时不编译Pods时面的静态库的问题，很奇怪的问题，也很难发现。
问题的来源可能就是***随便一点***，可能在试的时候，忘了改回来，悲剧就发生了。  
图中是正确配置，默认配置如此。

<iframe src="https://www.flickr.com/photos/shjborage/13759792423/player/" width="500" height="244" frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>