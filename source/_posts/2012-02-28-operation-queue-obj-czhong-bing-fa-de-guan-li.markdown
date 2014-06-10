---
layout: post
title: "Operation Queue Obj-C中并发的管理"
date: 2012-02-28 22:29
comments: true
categories: iOS
tags: iOS Obj-C 多线程
---

***Operation***是一种用面向对象封装的异步操作技术。operation即可以单独使用，也可与operation queue联合使用。因为是用Objective-C实现的，operation主要用于基于Cocoa的iOS/Mac OS X程序中。
operation queue是Cocoa对"并发dispatch queue"（C语言API）的封装。虽然dispatch queue总是以先进先出的方式执行任务，operation queue会考虑诸如依赖关系，优先级等因素来执行任务。

<!-- more -->

###简介

Operation对象就是**NSOperation**类的实例。NSOperation定义于Foundation framework之中，实现了一些基础功能，但并不完整，所以必须基于它派生新的子类，才能完成你想要的功能。同时，Foundation framework里提供了两种常用的NSOperation子类——*NSInvocationOperation*及*NSBlockOperation*，可供直接使用于代码中。

*NSInvocationOperation*用于将一个selector包装成operation对象。NSBlockOperation用于将一个或多个block对象包装成operation对象，多个block对象在operation对象中被并发的执行，只有当所有block完成之后operation才算完成。

所有的operation对象都支持以下特性，

*	operation对象之间可以建立图状的执行依赖关系
*	在整个operation完成后额外执行一个block（用于通知任务完成）
*	支持KVO方式来观察operation对象的状态
*	按优先级执行
*	中止正在执行的operation
*	operation对象的设计目的是帮助你提高程序的并发性，让你更专注于业务逻辑开始而非如何实现并发。


###并发 V.S. 非并发

可以将Operation对象加入operation queue来执行，也可以手动的调用operation对象的start方法来执行，但这样便不能保证operation与其它代码之间是并发的。isCurrent方法可以查看operation是以同步还是异步的方式运行的。默认情况下，返回值是NO，也就是说是以同步方式执行的。

若要实现并发的operation（即以异步方式执行），需要额外编写一些代码。比如，可能需要创建一个独立的线程，在这个线程里执行start。

对于大多数开发者而言并不需要去自己实现并发的operation，因为直接把operation对象加入operation queue即可，除非你想实现并发但又不想把对象加入到operation queue里。

若提交一个非并发的operation到operation queue里，queue自己会为你创建一个线程来执行，因此，事实上相对于其它operation这仍是异步的（除非operation之间有依赖关系？）。

####创建NSInvocationOperation对象

```
NSInvocationOperation* operation = [[[NSInvocationOperation alloc] initWithTarget:self selector:@selector(doSomething) object:nil] autorelease];
```

####创建NSBlockOperation对象

在创建时可以指定至少一个block对象，之后可以使用addExecutionBlock:方法来加入更多block。NSBlockOperation里的所有block会以默认优先级加入dispatch queue，等所有block结束之后，NSBlockOperation才结束。

####自定义operation对象

如果block operation和invocation operation都无法满足你的需求，是时候考虑自己实现一个operation对象了。NSOperation类已经提供了一些基础设备以供重用。
实现非并发operation对象比实现并发的要简单多了。所要做的工作就是实现operation的业务逻辑和响应“结束”事件。对于并发operation来说，必须用自己的代码替换现有的一些基础功能。
实现operation的业务逻辑只有两步，自定义initialization方法，实现main方法

*响应“结束”事件*

Operation在执行过程中可能被要求终止执行，终止的过程并非是野蛮的kill，而是需要在实现operation时加入一些检测点来“正常”结束。检测方法是在operation里调用isCancelled方法（由父类提供），isCancelled是个轻量级方法，所以你勿须在意性能问题，尽管调用。何时进行检测呢？以下是一些推荐的推测点，在真正开始执行业务逻辑之前对于业务逻辑中存在的循环结构，至少每次循环检测一次代码中任何比较容易处理结束事件的位置

###配置并发operation对象

虽然operation queue支持非并发operation，但绝大多数operation仍然是以异步方式运行的。然而，如果你是手工执行operation且仍然希望它们是异步的，你还是需要注意一些事项以确保能异步工作。

####与并发operation相关的一些方法

`start` - 所有并发operation对象必须重载该方法。如果是手工执行operation，你会调用start方法。因此，这里是整个operation的起点。注意，你不能在start中去调用父类的start，要完全重新实现。
main - 因为你是手工执行operation，这个方法就不是必须实现了。但是，把业务逻辑实现在main里，与设置动作分离能使软件的结构更加清晰。

`isExecuting/isFinished` - 并发operation必须正确维护自己的运行状态。这两个方法必须是线程安全的，因为它可能会在其它线程中调用。同时，支持KVO也是必须的。

`isConcurrent` - 对于并发operation永远是return YES。

####确保KVO兼容

NSOperation类兼容KVO标准，具体的属性包括，

```
isCancelled
isConcurrent
isExecuting
isFinished
isReady
dependencies
queuePriority
completionBlock
```

因此在继承NSOperation类时要注意保持KVO兼容，特别要注意isExecuting和isFinished。
isReady一般用于依赖关系的实现，若该属性返回NO，则operation永远不会被operation queue执行，当然前提是你在重用NSOperation提供的依赖关系检测逻辑，如果你实现了自己的依赖关系逻辑，这些就都不是问题了。

贴上一点实际用的代码：

```
//NSOperationQueue用来管理要处理的操作
//NSInvocationOperation 用来创建操作（上传的例子中有自定义操作）
playStopQueue = [[NSOperationQueue alloc] init];
[playStopQueue setMaxConcurrentOperationCount:1];
if([[playStopQueue operations] count] > 0 )
{
    NSInvocationOperation* lastOp = [[playStopQueue operations] lastObject];
    [stopOp addDependency:lastOp];
}
[playStopQueue addOperation:stopOp];
[playStopQueue cancelAllOperations];
[playStopQueue release];
playStopQueue = nil;
```

附上自定义Operation的示例：

http://ishare.iask.sina.com.cn/f/23306708.html
