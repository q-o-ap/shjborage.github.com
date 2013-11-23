---
layout: post
title: "iOS 用其它应用打开文件研究 Open In..."
date: 2013-2-17 17:34
comments: true
categories: iOS
---

今天上班的状态还没有完全恢复，就拿Reminders里面的提醒来看看，研究一些以前没时间弄的东西，就发现了这个。

<a href="http://www.flickr.com/photos/105999540@N03/10709167093/" title="2013-2-17-1 by EricShj, on Flickr"><img src="http://farm6.staticflickr.com/5535/10709167093_713ae846bd.jpg" width="381" height="28" alt="2013-2-17-1"></a>

这项功能开始不知道是哪个版本的iOS加入的，研究这些方法的时候才发现，原来从iOS3.2（iPad上市时）就开始支持了，真是惭愧啊。 下面慢慢道来  

1.注册应用支持的文档类型（会被系统管理起来）  
2.打开所用的ViewController

<!--more-->

####	1.注册类型，按需要添加 

附上本地的文档链接（搜索关键字：Uniform Type Identifier Concepts）
（file:///Users/shjborage/Library/Developer/Shared/Documentation/DocSets/com.apple.adc.documentation.AppleiOS5_0.iOSLibrary.docset/Contents/Resources/Documents/index.html#documentation/FileManagement/Conceptual/understanding_utis/understand_utis_conc/understand_utis_conc.html#//apple_ref/doc/uid/TP40001319-CH202-CHDHIJDE）

All Files这个文件类型，是从Dropbox里面抄来的，你懂的（http://www.cocoanetics.com/2013/01/open-in-all-files/）。

<a href="http://www.flickr.com/photos/105999540@N03/10708970444/" title="2013-2-17-2 by EricShj, on Flickr"><img src="http://farm6.staticflickr.com/5488/10708970444_870ea07b91.jpg" width="500" height="284" alt="2013-2-17-2"></a>


###	2.打开文件ViewController 
附本地文件链接：（搜索关键字：Previewing and Opening Files）file:///Users/shjborage/Library/Developer/Shared/Documentation/DocSets/com.apple.adc.documentation.AppleiOS5_0.iOSLibrary.docset/Contents/Resources/Documents/index.html#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/PreviewingandOpeningItems.html#//apple_ref/doc/uid/TP40010410-SW1

####UIDocumentInteractionController

不知道的话看一下文档就好了，还是比较容易使用。

<a href="http://www.flickr.com/photos/105999540@N03/10708900325/" title="2013-2-17-3 by EricShj, on Flickr"><img src="http://farm6.staticflickr.com/5478/10708900325_1a55a4d3d8.jpg" width="282" height="500" alt="2013-2-17-3"></a>

再有的话呢，就是HandleOpenUrl的用法了，这个相信大家都比较熟悉了，就不多说了。URL的内容是文件的路径，你自己想干什么都可以咯。
