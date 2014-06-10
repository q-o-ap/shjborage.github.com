---
layout: post
title: "对比iOS网络组件：AFNetworking VS ASIHTTPRequest"
date: 2013-10-20 22:55
comments: true
categories: iOS
tags: iOS Obj-C Network
---


在开发iOS应用过程中，如何高效的与服务端API进行数据交换，是一个常见问题。一般开发者都会选择一个第三方的网络组件作为服务，以提高开发效率和稳定性。这些组件把复杂的网络底层操作封装成友好的类和方法，并且加入异常处理等。

那么，大家最常用的组件是什么？这些组件是如何提升开发效率和稳定性的？哪一款组件适合自己，是 AFNetworking（AFN）还是 ASIHTTPRequest（ASI）？几乎每一个iOS互联网应用开发者都会面对这样的选择题，要从这两个最常用的组件里选出一个好的还真不是那么容易。

单单从两个控件版本提交的时间节点来看，AFN的第一个提交是2011年的1月1日，那个时候ASI早已是1.8+的版本了；而当AFN发布1.0版，2012年10月份的时候，ASI早早的已经停止更新了。这样看起来，AFN是ASI的继任者，似乎不存在之前提到的选择困难的问题，而事实并非如此。本文将从用法、功能、性能和原理几个方面对二者进行简单对比，看看二者之间到底存在着怎样的区别，到底应该如何选择。

<!-- more -->

*	首先，从推荐用法上就可以看出二者设计理念上大有不同。

###1、用法对比

<a href="http://www.flickr.com/photos/105999540@N03/10382705763/" title="2013-10-20-5 by EricShj, on Flickr"><img src="http://farm3.staticflickr.com/2867/10382705763_25114b14ed.jpg" width="464" height="309" alt="2013-10-20-5"></a>

图1，AFN的示例代码，发起请求（出自：Posts.m）

AFN官方推荐的使用方法是，为一系列相关的请求定义一个HTTPClient，共用一个BaseURL。每次请求把URL中除BaseURL的Path部分做为参数传给HTTPClient的静态方法，并注册一个Block用于回调。

<a href="http://www.flickr.com/photos/105999540@N03/10382705703/" title="2013-10-20-6 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3724/10382705703_90269d2bbe.jpg" width="464" height="143" alt="2013-10-20-6"></a>

图2，ASI示例代码，发起异步请求（出自：ASIHTTPRequestTests.m）  

ASI推荐使用方法就非常传统，每一个请求都由构造方法初始化一个（共享）实例，通过这个实例配置参数并发起请求。ASI最初使用delegate模式回调，在iOS SDK支持Block之后也提供了注册Block的实例方法。

以上引用的两段代码都出自各自项目的示例工程。对比两段代码可以很清楚的看出，同样是发起一个最普通的异步请求，使用AFN只需要调用一个静态方法，但代码可读性较差；而ASI的示例看起来更清晰，但需要调用多个实例方法才能完成一次请求。AFN的设计更加工程化，或者说对使用者更友好，而ASI的设计更经典，典型的OOP。
除了初级用法上的区别，二者的高级功能和对扩展的支持也颇有不同。

###2、高级功能

AFN只封装了一些常用功能，满足基本需求，而直接忽略了很多扩展功能。例如：AFN默认没有封装同步请求，如果开发者需要使用同步请求，则需要重写getPath:parameters:success:failure方法，对AFHTTPRequestOperation进行同步处理；而ASI则是直接通过调用一个startSynchronous方法。
此外AFN针对JSON、XML、PList和Image四种数据结构封装了各自处理器，开发者可以把处理器注册到操作队列中，直接在回调方法中获得格式化以后的数据。在示例工程中就使用了JSON处理器：把AFJSONRequestOperation注册到操作队列里。

<a href="http://www.flickr.com/photos/105999540@N03/10382500994/" title="2013-10-20-7 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7419/10382500994_16a83e90d4.jpg" width="464" height="192" alt="2013-10-20-7"></a>

图3，AFN示例代码，初始化自定义的HTTPClient（出自：AFAppDotNetAPIClient.m）

而ASI在这方面显得更原始，没有针对任何数据类型做特别封装，只是预留了各种接口和工具供开发者自行扩展。ASI比AFN提供更多扩展功能还有一个原因，它把许多内部用到的功能也抽象成类和方法。例如：
ASIHTTPRequestDataCompressor和ASIHTTPRequestDataDecompressor两个类，只用于压缩本地文件，构造POST Body和解压缩返回数据，但这两个类仍然被设计为独立功能，提供了对多种数据结构进行压缩和解压缩的方法。

对比二者的高级功能和对扩展的支持后，可以看出AFN把初级功能（或者叫常用功能）做到了90分。调用方式够简单，处理器够丰富，使用者用起来可以算是轻松加愉快。但它放弃了对高级功能的支持，要满足较复杂的需求，就要大费周折了，在这方面最多只有40分。而ASI显然不满足于做好初级功能，但为了提供更丰富的可扩展接口，导致初级功能用起来也要花上一些力气。虽然ASI单独提供了支持Amazon S3和Rackspace Cloud Files的控件，但对于生在红旗下的我朝开发者来说基本没用，所以在初级功能的支持上ASI能得个70分，牺牲了初级功能的易用性，换来的是良好的扩展性，在高级功能的使用上远远好于AFN，也能得个70分。

从使用角度对比过后，基本上对这两个项目有一个整体上的认识，再深入下去看看二者的性能如何。

###3、性能对比

我分别用AFN和ASI进行了测试，测试环境如下：iPhone5，联通3G信号全满，室内静止状态，请求国内双线机房独立服务器的静态文件，1~20K共20个文件，每个文件请求20次，记录从创建请求到完全下载文件的耗时，结果如下：

<a href="http://www.flickr.com/photos/105999540@N03/10382705653/" title="2013-10-20-8 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3715/10382705653_fa58c22d18.jpg" width="464" height="146" alt="2013-10-20-8"></a>

图4，AFN连续访问1 ~ 20K文件耗时

<a href="http://www.flickr.com/photos/105999540@N03/10382545196/" title="2013-10-20-9 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7296/10382545196_4c6f799013.jpg" width="464" height="146" alt="2013-10-20-9"></a>

图5，ASI连续访问1 ~ 20K文件耗时

图4是AFN的记录图，绿色为20次请求中耗时最久的一次，蓝色为耗时最短的一次，黄色为去除最大值和最小值的18次平均值。从这个图可以看出，AFN最开始创建对象耗时近2.5秒，随后稳定下来，在3K、7K、15K和20K时出现了抖动。图5是ASI做相同测试的结果，首次创建对象近2.25秒，略优于AFN，同样在5K、11K、13K、14K和16K发生了一些抖动，但抖动幅度似乎小于AFN，可见稳定性更好一些。
下边是把二者的测试结果放在一起的对比图，可以更直观的比较二者的区别。

<a href="http://www.flickr.com/photos/105999540@N03/10382500714/" title="2013-10-20-10 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7317/10382500714_0725ae4480.jpg" width="464" height="147" alt="2013-10-20-10"></a>

图6，ASI和AFN耗时最大值对比

图6的最大值对比可以更明显的看出二者的抖动对比，ASI略好一些。

<a href="http://www.flickr.com/photos/105999540@N03/10382525995/" title="2013-10-20-11 by EricShj, on Flickr"><img src="http://farm6.staticflickr.com/5476/10382525995_1a6cd98274.jpg" width="464" height="146" alt="2013-10-20-11"></a>

图7，ASI和AFN耗时最小值对比

图7的最小值对比可以看出，在每一个大小的测试中ASI的最佳性能似乎都要优于AFN。

<a href="http://www.flickr.com/photos/105999540@N03/10382705193/" title="2013-10-20-12 by EricShj, on Flickr"><img src="http://farm3.staticflickr.com/2880/10382705193_25f55ba5b3.jpg" width="464" height="147" alt="2013-10-20-12"></a>

图8，ASI和AFN耗时平均值对比

图8是耗时平均值的对比，更能够说明问题。文件小于12K的测试中ASI的性能优势并没有非常明显，超过12K以后，ASI优势开始明显起来，每一次请求都要比AFN节约20% ~ 30%，近0.1秒。同时从这张图上还可以看出，随着下载文件变大，请求耗时并不是线形增长的，这是由于一次请求大部分时间都消耗在建立连接上，而真正接收数据只占用了极少时间，这个问题不在本篇文章的讨论范围，所以不多说，有兴趣的读者可以[移步](http://segmentfault.com/t/ios)进一步讨论。

###4、原理分析

ASI的性能似乎全面优于AFN，那下边从二者的实现原理上看一下到底是什么原因造成这种差距。ASI基于CFNetwork框架开发，而AFN基于NSURL，底层的区别是导致二者性能差距的重要原因之一。

<a href="http://www.flickr.com/photos/105999540@N03/10382544986/" title="2013-10-20-13 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7325/10382544986_92a3d7bf99.jpg" width="464" height="289" alt="2013-10-20-13"></a>

图9，ASI和AFN以及底层框架的关系

我们知道所有网络通信的基础是Socket，一个Socket与另一个连接并传送数据。BSD Socket是一类最常见的Socket抽象接口。

Core Foundation框架中的CFSocket就是基于BSD Socket开发的。它几乎涵盖了BSD Socket的全部功能，更重要的是把Socket整合到事件的处理循环中。Core Founda-tion中较高层的CFStream是基于CFSocket开发的读写流支持。

CFNetwork是基于Core Foundation中CFStream的一个底层高性能网络框架，它由提供基础服务的CFSocketStream，支持HTTP协议的CFHTTP，基于CFHTTP用于身份认证的CFHTTPAuthentication和支持FTP协议的CFFTP组成。

正如图9所示，ASI是基于CFHTTP开发的一个组件；而AFN的基础——NSURL，也是基于CFNetwork开发的。也就是说ASI相比AFN更加底层，这就从一定程度上造成二者的性能差距。
另一个方面，虽然二者都使用NSOperation和NSOperationQueue实现但底层的区别也导致实现方式上有非常大的差别。

ASI的直接操作对象ASIHTTPRequest是NSOperation的子类，实现了NSCopying协议。在initialize和initWithURL:方法中初始化相关属性并配置一系列请求相关参数默认值。此外，ASIHTTPRequest还提供了一系列的实例方法用来配置请求对象。在异步请求的处理上，ASIHTTPRequest对象初始化结束后，在startAsynchronous方法中把对象加入共享操作队列。此后，包括创建CFHTTPMessageRef，也就是处理网络请求的主要对象（事实上是一个指向__CFHTTPMessage结构的指针），在内的所有操作都在ASIHTTPRequest对象所属的子线程中完成。

AFN的直接操作对象AFHTTPClient不同于ASI，是一个实现了NSCoding和NSCopying协议的NSObject子类。AFHTTPClient是一个封装了一系列操作方法的“工具类”，处理请求的操作类是一系列单独的，基于NSOperation封装的，AFURLConnectionOperation的子类。AFN的示例代码中通过一个静态方法，使用dispatch_once()的方式创建AFHTTPClient的共享实例，这也是官方建议的使用方法。在创建AFHTTPClient的初始化方法中，创建了OperationQueue并设置一系列参数默认值。在getPath:parameters:success:failure方法中创建NSURLRequest，以NSURLRequest对象实例作为参数，创建一个NSOperation，并加入在初始化发方中创建的NSOperationQueue。以上操作都是在主线程中完成的。在NSOperation的start方法中，以此前创建的NSURLRequest对象为参数创建NSURLConnection并开启连结。

在异步回调的处理上二者也有区别，ASI采取的是CFHTTP请求完成，直接回调ASIHTTPRequest的实例方法，通过储存的实例对象记录的信息完成Delegate模式或Block模式的回调。而AFN则直接使用了NSOperation的completionBlock属性。

这些实现方式也可以看出，ASI显得更加底层，并没有过多使用Cocoa框架中已经封装的API，而AFN则更加实用主义，逻辑简单清晰，大量使用了框架API。这一点也是造成二者性能差别的原因之一。

###总结

通过以上的对比，基本可以这样评价：AFN适合逻辑简单的应用，或者更适合开发资源尚不丰富的团队，因为AFN的易用性要比ASI好很多，而这样的应用（或团队）对底层网络控件的定制化要求也非常低。ASI更适合已经发展了一段时间的应用，或者开发资源相对丰富的团队，因为往往这些团队（或他们的应用）已经积累了一定的经验，无论是产品上还是技术上的。需求复杂度就是在这种时候高起来，而且底层订制的需求也越来越多，此时AFN就很难满足需求，需要牺牲一定的易用性，使用ASI作为网络底层控件。SegmentFault开源客户端现在被设计为一款简单的阅读客户端，几乎没有定制要求，因此，目前我选择了AFN作为网络控件。
以上对ASI和AFN两款最常用的iOS底层网络控件做了初步的介绍，要更深入的了解两款控件，还需要大家继续研究各自的源码。大家遇到任何关于iOS的技术问题都可以在[这里](http://segmentfault.com/t/ios)进行讨论。另外大家也可以持续关注SegmentFault的开源客户端，与更多的开发者共同探讨iOS开发技术。


作者简介

高嘉峻（微博：@gaosboy），SegmentFault.com联合创始人，杭州iOS开发者沙龙发起人，资深iOS开发者。 

http://www.infoq.com/cn/articles/afn_vs_asi
