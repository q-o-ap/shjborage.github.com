---
layout: post
title: "Does ARC support dispatch queues?"
date: 2013-12-01 01:48
comments: true
categories: iOS
---


**If your deployment target is lower than iOS 6.0 or Mac OS X 10.8**

You need to use dispatch_retain and dispatch_release on your queue. ARC does not manage them.

**If your deployment target is iOS 6.0 or Mac OS X 10.8 or later**

ARC will manage your queue for you. You do not need to (and cannot) use dispatch_retain or dispatch_release if ARC is enabled.

<!-- more -->

Details

Starting in the iOS 6.0 SDK and the Mac OS X 10.8 SDK, every dispatch object (including a dispatch_queue_t) is also an Objective-C object. This is documented in the <os/object.h> header file:

```
 * By default, libSystem objects such as GCD and XPC objects are declared as
 * Objective-C types when building with an Objective-C compiler. This allows
 * them to participate in ARC, in RR management by the Blocks runtime and in
 * leaks checking by the static analyzer, and enables them to be added to Cocoa
 * collections.
 *
 * NOTE: this requires explicit cancellation of dispatch sources and xpc
 *       connections whose handler blocks capture the source/connection object,
 *       resp. ensuring that such captures do not form retain cycles (e.g. by
 *       declaring the source as __weak).
 *
 * To opt-out of this default behavior, add -DOS_OBJECT_USE_OBJC=0 to your
 * compiler flags.
 *
 * This mode requires a platform with the modern Objective-C runtime, the
 * Objective-C GC compiler option to be disabled, and at least a Mac OS X 10.8
 * or iOS 6.0 deployment target.
```

This means you can store your queue in an NSArray or NSDictionary, or in a property with one of the strong, weak, unsafe_unretained, assign, or retain attributes. It also means that if you refer to your queue from a block, the block will retain the queue automatically.

So **if your deployment target is at least iOS 6.0 or Mac OS X 10.8**, and you have *ARC enabled*, ARC will retain and release your queue, and the compiler will flag any attempt to use dispatch_retain or dispatch_release as an error.

**If your deployment target is at least iOS 6.0 or Mac OS X 10.8**, and you have *ARC disabled*, you must manually retain and release your queue, either by calling dispatch_retain and dispatch_release, or by sending the queue retain and release messages (like [queue retain] and [queue release]).

For compatibility with old codebases, you can prevent the compiler from seeing your queue as an Objective-C object by defining OS_OBJECT_USE_OBJC to 0. For example, you can put this in your .pch file (before any #import statements):

```
#define OS_OBJECT_USE_OBJC 0
```

or you can add OS_OBJECT_USE_OBJC=0 as a preprocessor macro in your build settings. If you set OS_OBJECT_USE_OBJC to 0, ARC will not retain or release your queue for you, and you will have to do it yourself using dispatch_retain and dispatch_release.