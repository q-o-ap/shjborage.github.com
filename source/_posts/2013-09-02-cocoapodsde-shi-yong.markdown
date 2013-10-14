---
layout: post
title: "CocoaPods的使用"
date: 2013-09-02 11:00
comments: true
categories: iOS
---

#####问题来源：

在开发iOS的app时，我们时常需要引用第三方Open Source套件来简化开发，一般情况下，我们可以采用Add Files to的方式将其加入到目前的工程中，但也存在一些问题，比如版本演进的控制问题，当前工程引用的第三方库的清单等。

####什么是CocoaPods？

CocoaPods是一个依赖关系管理工具，用于管理Objective-C工程项目中各种Library。

####安装CocoaPods

由于CocoaPods是ruby的gem，需要首先安装RubyGems，mac电脑一般都已经安装好了ruby，但可能需要更新一下你的RubyGems（注意system前面是两个横线）
```
[plain] view plaincopy
sudo gem update --system  
```
安装command line工具
确保Xcode中安装了Command Line Tools，打开Xcode->Preferences->Downloads->Components，点一下“Install”按钮
安装CocoaPods

命令行输入：
```
[plain] view plaincopy
sudo gem install cocoapods  
pod setup  
```

然后就可以开始使用了。

用pod search Github上的套件名称就可以找到相应的说明，如
```
[plain] view plaincopy
pod search nimbus  
```

会输出

```
[plain] view plaincopy
-> Nimbus (1.0.0)  
   An iOS framework whose growth is bounded by O(documentation).  
   - Homepage: http://docs.nimbuskit.info/index.html  
   - Source:   https://github.com/jverkoey/nimbus.git  
   - Versions: 1.0.0, 0.9.3, 0.9.2, 0.9.1, 0.9.0 [master repo]  
   - Sub specs:  
         - Nimbus/Core (1.0.0)  
         - Nimbus/Badge (1.0.0)  
         - Nimbus/CSS (1.0.0)  
         - Nimbus/AttributedLabel (1.0.0)  
         - Nimbus/Interapp (1.0.0)  
         - Nimbus/Launcher (1.0.0)  
         - Nimbus/Models (1.0.0)  
         - Nimbus/NetworkControllers (1.0.0)  
         - Nimbus/NetworkImage (1.0.0)  
         - Nimbus/Overview (1.0.0)  
         - Nimbus/PagingScrollView (1.0.0)  
         - Nimbus/Photos (1.0.0)  
         - Nimbus/Operations (1.0.0)  
         - Nimbus/Operations/JSON (1.0.0)  
         - Nimbus/WebController (1.0.0)  
```

###使用CocoaPods管理第三方库

在工程目录下，新建一个空白的Podfile文档，然后在里面添加内容

```
[plain] view plaincopy
platform:ios  
pod 'AFNetworking', '1.1.0'  
```

保存时候，开始配置你的工程，终端中输入

```
[plain] view plaincopy
pod install  
```

执行完后，CocoaPods在工程目录创建了一个文件夹“Pods”，这个文件夹存放所有依赖的库，另外还创建了一个.workspace文件
注意：配置好之后必须用.workspace文件打开工程，而不是.xcodeproj.
后续：

在使用过程中，有可能会遇到类似下面的问题：

{% codeblock %}
[plain] view plaincopy
Resolving dependencies for target `default' (iOS 4.3)  
[!] AFNetworking (1.1.0) is not compatible with iOS 4.3.  
{% endcodeblock %}

解决方法，修改Podfile文档
```
[plain] view plaincopy
platform:ios, '5.0'  
```

以上可参考stackoverflow
转自：http://blog.csdn.net/numbbuaa/article/details/8549018