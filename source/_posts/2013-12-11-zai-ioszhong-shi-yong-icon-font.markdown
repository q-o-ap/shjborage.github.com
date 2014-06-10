---
layout: post
title: "在iOS中使用icon font及自定义字体"
date: 2013-12-11 21:42
comments: true
categories: iOS
tags: iOS UI
---


在开发阿里数据iOS版客户端的时候，由于项目进度很紧，项目里的所有图标都是用最平常的背景图片方案来实现。而为了要兼容普通屏与Retina屏的设备，苹果要求每个背景图都要以两种尺寸存(a.png和a@2x.png)，这让设计师们增加了成本，因为他们每次都得出两份背景图标。
 
现在在web开发上，icon font技术的应用很广泛，它不仅在解决多分辨率显示问题上很有成效，而且在使用它的时候还能降低不少设计和开发成本。
 
那么它能不能应用到ios开发上来呢？带着这个疑问，我在github上找到了FontasticIcons和ios-fontawesome，但是这两个OC包对icon资源封装都有限，而且扩展也不是很方便。
 
既然能在ios上使用icon font，那么怎么用呢？经过一番摸索，发现使用原理和自定义字体差不多，只有个别操作不太一样，接下来我给大家详细介绍一下。

<!-- more -->
 
###如何使用自定义字体

在讲icon font之前，首先先来看看普通自定义字体是如何在ios中使用的，两个原理是一样的。这里以KaushanScript-Regular为例：
 
####Step 1: 导入字体文件

将字体文件拖入项目（ios支持的字体格式有：.ttf、.otf，其他格式不确定）：
 
<a href="http://www.flickr.com/photos/shjborage/11323775276/" title="2013-12-11-01 by EricShj, on Flickr"><img src="http://farm3.staticflickr.com/2846/11323775276_b14a547aa1.jpg" width="500" height="354" alt="2013-12-11-01"></a>
 
然后再在项目的资源池中确认字体文件是否加入项目，打开xcode项目的Build Phases中查看：

<a href="http://www.flickr.com/photos/shjborage/11323859283/" title="2013-12-11-02 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3816/11323859283_dff602ee81.jpg" width="500" height="249" alt="2013-12-11-02"></a>
 
####Step 2: 配置.plist文件

在.plist文件中注册新加入的字体，.plist文件往往以“[appname]-Info.plist”的形式存在于“Supporting Files”文件夹内。 在.plist文件中添加新属性“Fonts provided by application”，该属性的值是一个数组，这意味着可以在这里注册多个字体。
 
<a href="http://www.flickr.com/photos/shjborage/11323790974/" title="2013-12-11-03 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3692/11323790974_9beda31ccb.jpg" width="500" height="209" alt="2013-12-11-03"></a>
 
####Step 3: 找到字体集名称

注册完，我们需要检测是否注册成功且取得新字体名称，检测方法就是把所有安装了的字体都打印出来，看看新注册的字体是否在里面：
 
```
for (NSString* family in [UIFont familyNames]) 
{ 
	NSLog(@"%@", family); 
	for (NSString* name in [UIFont fontNamesForFamilyName: family]) 
	{ 
         NSLog(@"  %@", name); 
    } 
} 
```
 
 
运行完，查看控制台里打印出的所有字体集中是否有新注册的字体，如果有，说明注册成功，并将字体名（在这里是“Kaushan Script”）记住留到后面用。

<a href="http://www.flickr.com/photos/shjborage/11323787836/" title="2013-12-11-04 by EricShj, on Flickr"><img src="http://farm6.staticflickr.com/5481/11323787836_e8fc5ed22a.jpg" width="500" height="392" alt="2013-12-11-04"></a>


###Step 4: 使用新字体

最后，就是使用你最新加入的字体啦：

```
UILabel *label = [[UILabel alloc] initWithFrame:CGRectMake(10, 60, 300, 50)]; 
label.font = [UIFont fontWithName:@"Kaushan Script" size:35]; 
label.text = @"icon font"; 
label.textColor = UIColorFromRGB(0xFF7300); 
[self.view addSubview:label]; 
```

效果：

<a href="http://www.flickr.com/photos/shjborage/11323858493/" title="2013-12-11-05 by EricShj, on Flickr"><img src="http://farm6.staticflickr.com/5471/11323858493_39eaf9269b.jpg" width="276" height="113" alt="2013-12-11-05"></a>

 
###开始使用icon font

图标字体也是字体，使用方式和上面所说的差不多，只是在套用上有些差别；这里拿fontello的图标字体库为例。
 
*	1.选择需要的图标
在fontello的图标字体库选择自己需要的图标，并下载生成的字体文件。 
*	2.按照上面的步骤将图标字体注册到项目中
*	3.找到图标对应的unicode码
使用FontLab Studio 5工具打开字体文件（比如fontello.ttf），就可以看到图标与unicode码之间的对应关系啦。

<a href="http://www.flickr.com/photos/shjborage/11323774246/" title="2013-12-11-06 by EricShj, on Flickr"><img src="http://farm3.staticflickr.com/2824/11323774246_65813b1aab.jpg" width="500" height="126" alt="2013-12-11-06"></a>
 

*	4.使用图标

```
UILabel *label = [[UILabel alloc] initWithFrame:CGRectMake(10, 60, 300, 50)]; 
label.font = [UIFont fontWithName:@"fontello" size:35]; 
label.text = @"\U0000E802 \U0000E801 \U0000E803 \U0000E804 \U0000E805 \U0000E81A"; 
label.textColor = UIColorFromRGB(0xFF7300); 
[self.view addSubview:label]; 
```
 
在Objective-C中，自定义的unicode码需要以“\U0000E802”这样的格式存在。
 
*	5.使用emoji表情库
这里还可以使用苹果emoji表情库的图标，这里不需要新加字体库，只要找到emoji图标和unicode之间的对应关系就好，但是这些图标都不是矢量图，缩放请自重。

```
UILabel *label5 = [[UILabel alloc] initWithFrame:CGRectMake(10, 480, 300, 50)]; 
label5.text = @"\U0000e42a\U0000e525\U0000e41c"; 
[self.view addSubview:label5]; 
```
 
*	6.效果

<a href="http://www.flickr.com/photos/shjborage/11323858703/" title="2013-12-11-07 by EricShj, on Flickr"><img src="http://farm6.staticflickr.com/5526/11323858703_820c5b3eef.jpg" width="366" height="500" alt="2013-12-11-07"></a>


*	7.使用自制图标字体
*	
如果上面介绍的图标库还满足不了你的需求，或者需要的图标分布在多个图标库而不能集中到一个字体文件中；那么你可能需要自己去制作图标字体文件了。
 
###总结

这样，在iOS开发上，不仅可以直接去开源图标库找现成的图标用到项目中，而且还可以轻松地改变图标的颜色、大小，相信可以解放不少设计师和工程师的工作量。
 
图标字体库
[fontello](http://fontello.com/)
etao图标字体库（这里有个问题要注意，etao的图标字体名为“Untitled1”，是不是生成字体的同学疏忽啦？）
IcoMoon
[emoji表情库](http://www.easyapns.com/iphone-emoji-alerts)
 
参考资料

[Common Mistakes With Adding Custom Fonts to Your iOS App](http://codewithchris.com/common-mistakes-with-adding-custom-fonts-to-your-ios-app/)

[Icon font 实践](http://ux.etao.com/posts/401)

FontasticIcons

ios-fontawesome

[Custom Unicode Characters in Objective-C](https://coderwall.com/p/wrb3pw)

[CSS3 icon font完全指南](http://www.qianduan.net/css3-icon-font-guide.html)

《转》
