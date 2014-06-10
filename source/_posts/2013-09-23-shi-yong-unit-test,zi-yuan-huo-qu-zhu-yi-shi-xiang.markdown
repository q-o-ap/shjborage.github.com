---
layout: post
title: "使用unit test，资源获取注意事项"
date: 2013-09-23 23:42
comments: true
categories: iOS
tags: iOS Obj-C 单元测试
---

### 引言
 使用XCode4.5 下使用unit test时，发现直接使用
 ```
 UIImage *imgA = [UIImage imageNamed:@"asdf"];
 NSString *resourcePath = [[NSBundle mainBundle] pathForResource:@”xxx.json”ofType:nil];
 ```
 得不到资源，调查一下原来是这样，怪不得人们写代码不想用imageNamed呢。
 
 <!-- more -->

###一、基础知识
####1，官方教程
　　单元测试包含“逻辑测试”和“应用测试”两种类型，分别对应不同的配置方式：
　　逻辑测试(Logic Unit Test):测试跟App运行过程无关的纯逻辑代码，比如随机数算法，数据结构一类的。
　　应用测试(Application Unit test):测试跟App运行环境相关的代码，比如目录、文件操作，Core Data存取等。
　　在Xcode4.5中，创建App时会自动创建Application Unit Test项目。 
####2，示例代码。
　　在Xcode documentation中，可以找到“iPhoneUnitTests”这个例子项目。这个项目会教你怎么写单元测试代码。
###二、进阶问题
####1，访问被测类
(在Logic Unit Test模式的要求，Application Unit Test不必设置) 比如在App中定义了FundManager.h这个接口，要想在单元测试中访问这个接口：  

	1，#import ”FundManager.h"    
	2，在单元测试的target中，Build Phases选项卡，Compile Sources区域中，增加FundManager.m文件。  

　　否则会在链接阶段找不到FundManager接口。
　　
####2，向单元测试目标中添加资源
　　比如要测试的某个接口需要UIImage对象，需要一张图片来初始化UIImage对象，直接调用[UIImage imageNamed:@”xxx”]是不行的，因为这个接口要从App的main bundle中加载图像，不能直接访问添加到单元测试项目中的资源，下面代码演示了两个bundle目录的不同：

```
Resource Paths are Different!
Many assumptions that your bundle is the main bundle will cause problems when testing. (Especially when adding tests to existing code) Look at the difference in bundles, the main bundle isn’t what you’d expect in a unit test.
NSString *mainBundlePath = [[NSBundle mainBundle] resourcePath];
NSString *directBundlePath = [[NSBundle bundleForClass:[self class]] resourcePath];
NSLog(@”Main Bundle Path: %@”, mainBundlePath);
NSLog(@”Direct Path: %@”, directBundlePath);
NSString *mainBundleResourcePath = [[NSBundle mainBundle] pathForResource:@”Frame.png”ofType:nil];
NSString *directBundleResourcePath = [[NSBundle bundleForClass:[selfclass]] pathForResource:@”Frame.png” ofType:nil];
NSLog(@”Main Bundle Path: %@”, mainBundleResourcePath);
NSLog(@”Direct Path: %@”, directBundleResourcePath);
```
解决方法：
```
Load resources using the bundle for the current classNSString *imagePath = [[NSBundle bundleForClass:[self class]] pathForResource:theImageName ofType:nil]; 
UIImage *image = [UIImage imageWithContentsOfFile:imagePath];
```