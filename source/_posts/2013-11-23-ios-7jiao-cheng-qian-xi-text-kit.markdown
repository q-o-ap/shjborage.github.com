---
layout: post
title: "iOS 7教程：浅析Text Kit"
date: 2013-11-23 22:06
comments: true
categories: iOS
---

转自[破船](http://beyondvincent.com/blog/2013/11/12/121-brief-analysis-text-kit/)的博客


Text Kit是iOS 7中引入的一个新功能，非常值得开发者使用，下面先看看本文的目录结构：

*	什么是Text Kit
*	Text Kit架构
*	Text Kit特点
*	Text Kit功能概述
*	Text Kit中重要的一些对象
*	Text Kit示例
*	小结
*	推荐Text Kit学习资源

<!-- more -->

 
###什么是Text Kit

在iOS7中，苹果引入了Text Kit--Text Kit是一个快速而又现代化的文字排版和渲染引擎。Text Kit在UIKit framework中的定义了一些类和相关协议，它最主要的作用就是为程序提供文字排版和渲染的功能。在程序中，通过Text Kit可以对文字进行存储(store)、布局(lay out)，以及用最精细的排版方式(例如文字间距、换行和对齐等)来显示文本内容。 苹果引入Text Kit的目的并非要取代已有的Core Text，Core Text的主要作用也是用于文字的排版和渲染中，它是一种先进而又处于底层技术，如果我们需要将文本内容直接渲染到图形上下文(Graphics context)时，从性能和易用性来考虑，最佳方案就是使用Core Text。而如果我们直接利用苹果提供的一些控件(例如UITextView、UILabel和UITextField等)对文字进行排版，无疑就是借助于UIkit framework中Text Kit提供的API。
 
###Text Kit架构

下面，我们通过图1(此图来自WWDC2013 Session 210)来了解一下Text Kit的架构。图1是基于iOS 7绘制的，从图中，我们可以看到Text Kit是基于Core Text构建的，它通过Core Text与Core Graphics进行交互。而UI控件(UILabel、UITextField和UITextView)则构建于Text Kit之上，可见这些文本控件可以利用Text Kit提供的API来对文字进行排版和渲染处理。 从图中我们也可以看到SDK提供的UIWebView是基于WebKit的，它不能使用Text Kit提供的功能。
 
<a href="http://www.flickr.com/photos/105999540@N03/11028898935/" title="2013-11-24-1 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7439/11028898935_e836a8c934.jpg" width="500" height="211" alt="2013-11-24-1"></a>

图1 Text Kit在iOS 7 SDK中的位置

我们再来看看图1中的相关组件在iOS6里面是如何对应的，如图2所示，可以看出在iOS 6中是没有Text Kit，并且UILabel、UIText和UITextView是基于String Drawing和WebKit构建的。其中String Drawing是与Core Graphics直接通讯。
 
<a href="http://www.flickr.com/photos/105999540@N03/11029000326/" title="2013-11-24-2 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7430/11029000326_f1345af879.jpg" width="500" height="242" alt="2013-11-24-2"></a>

图2 在iOS 6中并没有Text Kit

###Text Kit特点

从上面的介绍中，我们可以了解到Text Kit在UIKit中的作用非常重要。Text Kit在实际开发中具有如下特点：

*	1.在UI控件中Text Kit完全掌控着文字的排版和渲染
*	2.UITextView、UITextField和UILabel是构建于Text Kit之上的
*	3.能够与动画、UICollectionView和UITableView做到无缝集成
*	4.Text Kit具有这样一些能力：Subclassing、Delegation和Notifcation。
 
###Text Kit功能概述

下面我们看看通过Text Kit，都能实现那些功能(这里列出了是一些常用和重要功能)：

*	1.对文字进行分页或多列排版
*	2.支持文字的换行、折叠和着色等处理
*	3.可以调整字与字之间的距离、行间距、文字大小、指定特定的字体
*	4.支持富文本编辑，可以自定义文字截断
*	5.支持凸版印刷效果(letterpress)
*	6.支持数据类型的检测(例如链接、附件等)
 
如图3，是利用Text Kit对文字做的分页排版

<a href="http://www.flickr.com/photos/105999540@N03/11028898985/" title="2013-11-24-3 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7420/11028898985_e42af249cf.jpg" width="500" height="303" alt="2013-11-24-3"></a>

图3 利用Text Kit做的分页排版效果

再看图4，是利用Text Kit做的换行处理，其中对某个路径范围做了排除。

<a href="http://www.flickr.com/photos/105999540@N03/11029105983/" title="2013-11-24-4 by EricShj, on Flickr"><img src="http://farm6.staticflickr.com/5530/11029105983_dba9ffd9fb.jpg" width="500" height="359" alt="2013-11-24-4"></a>

图4 利用Text Kit做的换行处理效果

再来看看利用Text Kit做的凸版印刷效果，如图5所示
 
<a href="http://www.flickr.com/photos/105999540@N03/11029054444/" title="2013-11-24-5 by EricShj, on Flickr"><img src="http://farm3.staticflickr.com/2885/11029054444_fd67572185.jpg" width="500" height="365" alt="2013-11-24-5"></a>

图5 利用Text Kit做的凸版印刷效果

###Text Kit中重要的一些对象

下面我们来看看Text Kit中重要的几个对象。
 
<a href="http://www.flickr.com/photos/105999540@N03/11029054134/" title="2013-11-24-6 by EricShj, on Flickr"><img src="http://farm3.staticflickr.com/2849/11029054134_87158b12d4.jpg" width="500" height="293" alt="2013-11-24-6"></a>

图6 Text Kit中重要的几个对象

如图6所示，Text Kit中主要有4个重要的对象。

*	1.Text View是用来显示文本内容的控件，主要包括UILabel、UITextView和UITextField。
*	2.Text containers对应着NSTextContainer类。NSTextContainer定义了文本可以排版的区域。一般来说，都是矩形区域，当然，也可以根据需求，通过子类化NSTextContainer来创建别的一些形状，例如圆形、不规则的形状等。NSTextContainer不仅可以创建文本可以填充的区域，它还维护着一个数组——该数组定义了一个区域，排版的时候文字不会填充该区域，因此，我们可以在排版文字的时候，填充非文本元素(例如图片，如图4所示)。
*	3.Layout manager对应着NSLayoutManager类。该类负责对文字进行编辑排版处理——通过将存储在NSTextStorage中的数据转换为可以在视图控件中显示的文本内容，并把统一的字符编码映射到对应的字形(glyphs)上，然后将字形排版到NSTextContainer定义的区域中。
*	4.Text storage对应着NSTextStorage类。该类定义了Text Kit扩展文本处理系统中的基本存储机制。NSTextStorage继承自NSmutableAttributedString，主要用来存储文本的字符和相关属性。另外，当NSTextStorage中的字符或属性发生了改变，会通知NSLayoutManager，进而做到文本内容的显示更新。
 
通常情况下，NSTextStorage、NSLayoutManager和NSTextContainer是一一对应的。如图7所示关系：
 
<a href="http://www.flickr.com/photos/105999540@N03/11028999346/" title="2013-11-24-7 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3722/11028999346_3d78e9639e.jpg" width="500" height="41" alt="2013-11-24-7"></a>

图7 普通排版

当然，如果需要将文字显示为多列，或多页，可以按照如图8所示关系——使用多个NSTextContainer。

<a href="http://www.flickr.com/photos/105999540@N03/11029104793/" title="2013-11-24-8 by EricShj, on Flickr"><img src="http://farm6.staticflickr.com/5472/11029104793_ed7de38158.jpg" width="500" height="64" alt="2013-11-24-8"></a> 

图8 多页或者多列排版

如果针对不同的排版方式，则可以使用多个NSLayoutManager，如图9所示
 
<a href="http://www.flickr.com/photos/105999540@N03/11029104933/" title="2013-11-24-9 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3826/11029104933_824d194d5f.jpg" width="500" height="59" alt="2013-11-24-9"></a>

图9 不同的排版方式

如图10所示，通过形象的方式，对UITextView的组成做了分解。通常，我们在设备上只能看到最右边的文本显示界面，而内部的NSTextStorage、NSLayoutManager和NSTextContainer是看不出来的。通常由NSLayoutManager从NSTextStorage中读取出文本数据，然后根据一定的排版方式，将文本排版到NSTextContainer中，再由NSTextContainer结合UITextView将最终效果显示出来。
 
<a href="http://www.flickr.com/photos/105999540@N03/11029105053/" title="2013-11-24-10 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7294/11029105053_875c954204.jpg" width="500" height="319" alt="2013-11-24-10"></a>

图10 UITextView的分解

###Text Kit示例

前面对Text Kit做了一些介绍，下面我们配合一个例子(图文排版)，来进一步加深对Text Kit的认识。具体实现步骤如下：

*	1.打开Xcode 5，新建一个Single View Application模板的程序，将工程命名为ExclusionPath。
*	2.打开Main.storyboard文件，然后再默认View Controller的View里面分别添加一个UITextView和UIImageView。并将这两个控件连接到ViewController.h中(名称分别为textView何imageView)。然后给textView设置一些字符串，imageView设置一个图片。
*	3.打开ViewController.m文件，找到viewDidLoad方法，用如下代码替换该方法：

```
- (void)viewDidLoad 
{ 
    [super viewDidLoad]; 
  
    //创建一个平移手势对象，该对象可以调用imagePanned：方法 
    UIPanGestureRecognizer *panGes = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(imagePanned:)]; 
    [self.imageView addGestureRecognizer:panGes]; 
  
    self.textView.textContainer.exclusionPaths = @[[self translatedBezierPath]]; 
} 
```

在上面的代码中，给imageView添加了一个平移手势。另外通过调用translatedBezierPath方法，给textView的textContainer设置exclusionPaths属性值。表示需要排除的区域（也就是图片在排版中显示的位置）。
 
*	4.下面来看一下translatedBezierPath方法的实现，如下代码所示

```
- (UIBezierPath *)translatedBezierPath 
{ 
    CGRect butterflyImageRect = [self.textView convertRect:self.imageView.frame fromView:self.view]; 
    UIBezierPath *newButterflyPath = [UIBezierPath bezierPathWithRect:butterflyImageRect]; 
  
    return newButterflyPath; 
}
```
 
在上面的代码中，利用imageView的frame属性创建了一个UIBezierPath，然后将该值返回。 

*	5, 还记得第3步中创建的平移手势吗。里面有一个action需要实现imagePanned:，下面来看看这个方法的实现：
	
```
- (void)imagePanned:(id)sender 
{ 
    if ([sender isKindOfClass:[UIPanGestureRecognizer class]]) { 
        UIPanGestureRecognizer *localSender = sender; 
  
        if (localSender.state == UIGestureRecognizerStateBegan) { 
            self.gestureStartingPoint = [localSender translationInView:self.textView]; 
            self.gestureStartingCenter = self.imageView.center; 
        } else if (localSender.state == UIGestureRecognizerStateChanged) { 
            CGPoint currentPoint = [localSender translationInView:self.textView]; 
  
            CGFloat distanceX = currentPoint.x - self.gestureStartingPoint.x; 
            CGFloat distanceY = currentPoint.y - self.gestureStartingPoint.y; 
  
            CGPoint newCenter = self.gestureStartingCenter; 
  
            newCenter.x += distanceX; 
            newCenter.y += distanceY; 
  
            self.imageView.center = newCenter; 
  
            self.textView.textContainer.exclusionPaths = @[[self translatedBezierPath]]; 
        } else if (localSender.state == UIGestureRecognizerStateEnded) { 
            self.gestureStartingPoint = CGPointZero; 
            self.gestureStartingCenter = CGPointZero; 
        } 
    } 
} 
```
 
在上面的代码中首先根据平移的距离来设置imageView的位置，然后利用translatedBezierPath方法重新计算了一下排除区域。 

*	6, 至此代码编写完毕，下面来运行程序，看看实际效果。如图11所示：
 
<a href="http://www.flickr.com/photos/105999540@N03/11028998926/" title="2013-11-24-11 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3709/11028998926_73393ee43a.jpg" width="332" height="500" alt="2013-11-24-11"></a>

图11 运行效果
 
###小结

实际上，上面的示例，只是揭秘了Text Kit功能的冰山一角。从iOS7及以后的版本中，Text Kit在UIKit framework里面占据重要的地位，Text Kit在文字处理方面，具有非常强大的功能，并且开发者可以对Text Kit进行定制和扩展。据悉，苹果利用了2年的时间来开发Text Kit，相信这对许多开发者来说都是福音。
 
推荐Text Kit学习资源
更多关于Text Kit的学习资料，请参考下面的内容：
wwdc视频:
Introducing Text Kit
Advanced Text Layouts and Effects with Text Kit
Using Fonts with Text Kit
 
苹果官方参考文档
Text Programming Guide for iOS.pdf
NSLayoutManager Class Reference for iOS.pdf
NSLayoutManagerDelegate Protocol Reference for iOS.pdf
NSTextContainer Class Reference for iOS.pdf
NSTextStorage Class Reference for iOS.pdf
NSTextStorageDelegate Protocol Reference for iOS.pdf
 
苹果官方示例：
IntroToTextKit

