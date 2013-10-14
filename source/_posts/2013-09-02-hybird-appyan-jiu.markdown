---
layout: post
title: "Hybird App研究"
date: 2013-09-02 09:02
comments: true
categories: iOS
---


####hybrid App

“云”时代的来临正在改变App和运营团队之间的关系，一场不能避免的变革正在进行。鉴于移动终端的局限性，移动终端上的APP由本地化应用(Native App)，到混合型应用（Hybrid APP），再到基于WEB的应用Web App，这一连串的变化都源于技术的更新和市场的需要。

Hybrid App是指介于web-app、native-app这两者之间的app,它虽然看上去是一个Native App，但只有一个UI WebView，里面访问的是一个Web App，比如街旁网最开始的应用就是包了个客户端的壳，其实里面是HTML5的网页，后来才推出真正的原生应用。再彻底一点的，如掌上百度和淘宝客户端Android版，走的也是Hybrid App的路线，不过掌上百度里面封装的不是WebView，而是自己的浏览内核，所以体验上更像客户端，更高效。

汽车有混合动力Hybrid，移动应用同样也有混合模式。Hybrid App（混合模式移动应用）兼具“Native App良好用户交互体验的优势”和“Web App跨平台开发的优势”。很多人不知道市场上一些主流移动应用都是基于Hybrid App的方式开发，比如国外有Facebook、国内有百度搜索等。但究竟什么是Hybrid App？如何定义？

Hybrid App：Hybrid App is a mobile application that is coded in both browser-supported language and computer language. They are available through application distribution platforms such as the Apple App Store, Google Play etc. Usually, they are downloaded from the platform to a target device, such as iPhone, Android phone or Windows Phone. The subscribers need to install to run them.

我们来拆解一下里面的含义：

*	1、mobile application：Hybrid App就是一个移动应用
*	2、both browser-supported language and computer language：同时使用网页语言与程序语言编写
*	3、available through application distribution platforms：通过应用商店进行分发
*	4、a target device：区分目标平台
*	5、install to run：用户需要安装使用
*	
综合一下就是：“Hybrid App同时使用网页语言与程序语言开发，通过应用商店区分移动操作系统分发，用户需要安装使用的移动应用”。总体特性更接近Native App但是和Web App区别较大。只是因为同时使用了网页语言编码，所以开发成本和难度比Native App要小很多。因此说，Hybrid App兼具了Native App的所有优势，也兼具了Web App使用HTML5跨平台开发低成本的优势

####Hybrid App为什么会兴起

Hybrid App的兴起是现阶段移动互联网产业的一种偶然。移动互联网的热潮刮起后，众多公司前赴后继的进入。但是很快发现移动应用的开发人员太少，所以导致疯狂的人才争夺。市场机制下移动应用开发人才的待遇扶摇直上，最终变成众多企业无法负担养一个具备跨平台开发能力的专业移动应用开发团队。而HTML5的出现让Web App露出曙光，HTML5开发移动应用的跨平台和廉价优势让众多想进入移动互联网领域的公司开始心动。可是当下基于HTML5的Web App更是雾里看花，在用户入口习惯、分发渠道和应用体验这三个核心问题没解决之前，Web App也很难得以爆发。正是在这样是机缘巧合下，基于HTML5低成本跨平台开发优势又兼具Native App特质的Hybrid App技术杀入混战，并且很快吸引了众人的目光。大幅的降低了移动应用的开发成本，可以通过现有应用商店模式发行，在用户桌面形成独立入口等等这些，让Hybrid App成为解决移动应用开发困境不错的选择，也成为现阶段Web App的代言人。Hybrid App像刺客一样，在Native App和Web App混战之时，偶然间的在移动应用开发领域占有了一席之地。

如何实现网页语言与程序语言的混合

Hybrid App通常分为三种类型：多View混合型，单View混合型，Web主体型。

#####多View混合型
即Native View和Web View独立展示，交替出现。目前常见的Hybrid App是Native View与WebView交替的场景出现。这种应用混合逻辑相对简单。即在需要的时候，将WebView当成一个独立的View（Activity）运行起来，在WebView内完成相关的展示操作。这种移动应用主体通常是Native App，Web技术只是起到补充作用。开发难度和Native App基本相当。

#####单View混合型

即在同一个View内，同时包括Native View和Web View。互相之间是覆盖（层叠）的关系。这种Hybrid App的开发成本较高，开发难度较大，但是体验较好。如百度搜索为代表的单View混合型移动应用，既可以实现充分的灵活性，又能实现较好的用户体验。

#####Web主体型

即移动应用的主体是Web View，主要以网页语言编写，穿插Native功能的Hybrid App开发类型。这种类型开发的移动应用体验相对而言存在缺陷，但整体开发难度大幅降低，并且基本可以实现跨平台。Web主体型的移动应用用户体验的好坏，主要取决于底层中间件的交互与跨平台的能力。国外的appMobi、PhoneGap国内的AppCan和Rexsee都属于Web主体型移动应用中间件。其中Rexsee不支持跨平台开发。appMobi和PhoneGap除基础的底层能力更多是通过插件（Plugins）扩展的机制实现Hybrid。而AppCan除了插件机制，还提供了大量的单View混合型的接口来完善和弥补Web主体型Hybrid App体验差的问题，接近Native App的体验。

  
从分析可见，Hybrid App中的Web主体型只要能够解决用户体验差的问题，就可以变成最佳Hybrid App解决方案类型。

####Hybrid App的瓶颈与未来
国内外Hybrid App的开发框架众多。如何选择又成为一个难题。下面对开发者比较关心的集中知名跨平台开发移动应用中间件进行列表和对比，以便选择最适合您的移动应用中间件。

Hybrid App的瓶颈与未来

国内外Hybrid App的开发框架众多。如何选择又成为一个难题。下面对开发者比较关心的集中知名跨平台开发移动应用中间件进行列表和对比，以便选择最适合您的移动应用中间件。

  
PhoneGap是相对比较早进入公众视线的一种选择。但是，开发者简单的基于PhoneGap来开发移动应用肯定会发现结果和Web App比较差的用户体验类似。这也是为什么基于PhoneGap有实用性的移动应用主要集中在iOS上。可是PhoneGap这种现状弱化了HTML5的跨平台价值。

AppCan在技术架构上和PhoneGap类似是Web主体型中间件，但是通过结合了一些原生交互效果能够达到iOS、Android平台都比较一致的用户体验。但是相比PhoneGap的开源，AppCan相对封闭的路线显得过于谨慎。

Titanium是一种基于翻译机制的跨平台中间件，能够开发出具有Native体验的移动应用，但是因为翻译机制的限制导致移动应用开发不能像真正的HTML5开发一样灵活。哪怕一个按钮也不能像普通HTML一样来编写，而必须按照Titanium约定的特定格式。

Salama是全新研发的一套Hybrid APP和云端服务整合的开发套件。在终端，一共有三种开发模式：高度混合模式、JS模式和低度混合模式。在低度混合模式下，需要追求终端速度和显示效果的开发者，可以在不改变原有的构架思路的前提下进行开发。在JS模式下，所有的画面显示及业务逻辑均由JS程序实现，对于原来的基于WEB的开发者，只要熟悉JS、CSS、HTML的前端工程师就可以轻松构建自己的应用。在高度混合模式下，原生和HTML5可以随意组合，可以为开发者在进行大型商业软件开发的过程中提供最高的性价比。不仅如此，在云端Salama还提供了一整套云服务，涵盖了业务处理、数据库操作、文件等资源的存储分发等服务。Salama已经为多家公司提供了方案，知名客户有TOSHIBA-AIRCON、GEDORE等品牌商，同时也在Salama平台上构造了Ishow企业电子目录产品服务。[1-2]

Hybrid App这个领域虽然还处于比较初期的阶段，但是已经有很多优秀的公司和技术团队在致力于跨平台开发移动应用中间件技术的研究，给了开发者众多选择。开发者可以根据实际的项目需求来选择中间件。Web App虽被浏览器厂商和搜索引擎公司所推崇，但存在用户体验差、盈利模式不明确等现阶段无法解决的问题，或最终夭折。Hybrid App正在被越来越多的公司和开发者所认同，势必会成为新世界的王。

http://baike.baidu.com/view/8488720.htm

Web App和Native App 谁将是未来: 
http://www.cnblogs.com/mofish/archive/2011/07/18/2109565.html

国内领先的Hybrid App框架 AppCan:   http://www.appcan.cn/