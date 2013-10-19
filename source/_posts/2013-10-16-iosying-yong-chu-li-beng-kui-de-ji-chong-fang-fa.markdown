---
layout: post
title: "iOS应用处理崩溃的几种方法"
date: 2013-10-16 23:31
comments: true
categories: iOS
---

在开发IOS app的过程中,XCode在遇到程序崩溃时打印的信息一般时比较少的。
在iOS7时，这个情况好像有些一些变化，但不太明确，也不太稳定。

###1.在XCode中使用一个通用断点
在XCode中打开工程，在左边的工程导航栏中选中断点,点击下放的＋号,选择“Add Exception BreakPoint”。点done即可。

###2.开启NSZombieEnabled等调试选项
XCode->Product->Scheme->Edit Scheme->Run XXX.app->Arguments
在Environment Variables中增加下面属性并都配置为YES。

```
MallocStackLogging
MallocStackLoggingNoCompact
NSAutoreleaseFreedObjectCheckEnabled
NSDebugEnabled
NSZombieEnabled
```

在实际使用中发现，使用了上述配置之后，如果能在模拟器上运行效果更佳。比如,真机调试时遇到过如下错误：

```
-[CALayer retain]: message sent to deallocated instance 0x1d418a30
```

使用网上很多地方都提到的方法：

```
info malloc-history 0x1d418a30
XCode提示:Undefined info command: "malloc-history".  Try "help info".
```
针对这种情况可以参考:http://stackoverflow.com/questions/3851565/using-gdb-info-malloc-command-within-xcode-iphone-dev,里面就说我们应该在模拟器中使用类似malloc-history的gdb调试功能。

**其中最主要的还是** *NSZombieEnabled*, 这个解决EXC_BAD_ACCESS还是很有用的。
具体参考这个：http://www.cocoachina.com/macdev/objc/2011/0219/2661.html

###3.增加异常处理[理论上和"在XCode中使用一个通用断点"效果类似]
写iOS代码的时候,经常会遇到程序崩溃在main函数的入口处并出现类似"First throw call stack"这样的错误。为了更好的知道崩溃的原因,我们可以在AppDelegate中定义一个方法, 用于处理异常:

```
void caughtException(NSException *exception) 
{  
	NSLog(@"CRASH: %@", exception);  
	NSLog(@"Stack Trace: %@", [exception callStackSymbols]);  
	// Internal error reporting 
} 
```
另外，在打印时，也可以使用GTM，GTMStackTraceFromException(exception)，跟系统的类似。

然后在应用启动时，设置这个方法作为自己的自定义异常回调： 

```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions 
{  
	NSSetUncaughtExceptionHandler(& caughtException); 
} 
```
在定义了这个回调之后，崩溃是的控制台信息一般都会一目了然，我们甚至可以看出是那个类的哪一行出问题了。

###4.signal处理，并提示。

启动信号处理

```
	signal(SIGABRT, signalHandler);
    signal(SIGILL, signalHandler);
    signal(SIGSEGV, signalHandler);
    signal(SIGFPE, signalHandler);
    signal(SIGBUS, signalHandler);
    signal(SIGPIPE, signalHandler);
```

其它相关代码
{% codeblock %}
#include <libkern/OSAtomic.h>
#include <execinfo.h>

NSString * const UncaughtExceptionHandlerSignalExceptionName = @"UncaughtExceptionHandlerSignalExceptionName";
NSString * const UncaughtExceptionHandlerSignalKey = @"UncaughtExceptionHandlerSignalKey";
NSString * const UncaughtExceptionHandlerAddressesKey = @"UncaughtExceptionHandlerAddressesKey";

volatile int32_t UncaughtExceptionCount = 0;
const int32_t UncaughtExceptionMaximum = 10;
const NSInteger UncaughtExceptionHandlerSkipAddressCount = 0;
const NSInteger UncaughtExceptionHandlerReportAddressCount = 100;

+ (NSArray *)backTrace
{
  void* callstack[1024];
  int frames = backtrace(callstack, 1024);
  char **strs = backtrace_symbols(callstack, frames);
  
  int i;
  NSMutableArray *backtrace = [NSMutableArray arrayWithCapacity:frames];
  for (i = UncaughtExceptionHandlerSkipAddressCount;
       i < UncaughtExceptionHandlerSkipAddressCount + UncaughtExceptionHandlerReportAddressCount;
       i++)
  {
    if (frames <= i)
      break;
    [backtrace addObject:[NSString stringWithUTF8String:strs[i]]];
  }
  
  free(strs);
  
  return backtrace;
}

- (void)alertView:(UIAlertView *)anAlertView clickedButtonAtIndex:(NSInteger)anIndex
{
  if (anIndex == 0) {
    _dismissed = YES;
  }
}

- (void)handleException:(NSException *)exception
{
  UIAlertView *alert = [[UIAlertView alloc] initWithTitle:NSLocalizedString(@"Unhandled exception", nil)
                                                  message:[NSString stringWithFormat:NSLocalizedString(@"You can try to continue but the application may be unstable.\n" @"%@\n%@", nil), [exception reason], [[exception userInfo] objectForKey:UncaughtExceptionHandlerAddressesKey]]
                                                 delegate:self
                                        cancelButtonTitle:NSLocalizedString(@"Quit", nil)
                                        otherButtonTitles:NSLocalizedString(@"Continue", nil), nil];
  
  [alert show];
  
  CFRunLoopRef runLoop = CFRunLoopGetCurrent();
  CFArrayRef allModes = CFRunLoopCopyAllModes(runLoop);
  
  while (!_dismissed) {
    for (NSString *mode in (NSArray *)CFBridgingRelease(allModes)) {
      CFRunLoopRunInMode((CFStringRef)CFBridgingRetain(mode), 0.001, false);
    }
  }
  
  CFRelease(allModes);
  
  NSString *log = [NSString stringWithFormat:@"%@\n\n info:\n%@", exception, exception.userInfo];
  [[DebugUtil sharedDebug] LogException:log];
  
  NSSetUncaughtExceptionHandler(NULL);
  signal(SIGABRT, SIG_DFL);
  signal(SIGILL, SIG_DFL);
  signal(SIGSEGV, SIG_DFL);
  signal(SIGFPE, SIG_DFL);
  signal(SIGBUS, SIG_DFL);
  signal(SIGPIPE, SIG_DFL);
  
  if ([[exception name] isEqual:UncaughtExceptionHandlerSignalExceptionName]) {
    kill(getpid(), [[[exception userInfo] objectForKey:UncaughtExceptionHandlerSignalKey] intValue]);
  } else {
    [exception raise];
  }
}

NSString * getAppInfo()
{
  NSString *appInfo = [NSString stringWithFormat:@"App : %@ %@(%@)\nDevice : %@\nOS Version : %@ %@\nUDID : %@\n",
                       [[NSBundle mainBundle] objectForInfoDictionaryKey:@"CFBundleDisplayName"],
                       [[NSBundle mainBundle] objectForInfoDictionaryKey:@"CFBundleShortVersionString"],
                       [[NSBundle mainBundle] objectForInfoDictionaryKey:@"CFBundleVersion"],
                       [UIDevice currentDevice].model,
                       [UIDevice currentDevice].systemName,
                       [UIDevice currentDevice].systemVersion,
                       /*[UIDevice currentDevice].uniqueIdentifier*/@""];
  return appInfo;
}

void signalHandler(int signal)
{
  int32_t exceptionCount = OSAtomicIncrement32(&UncaughtExceptionCount);
  
  if (exceptionCount > UncaughtExceptionMaximum) {
    return;
  }
  
  NSMutableDictionary *userInfo = [NSMutableDictionary dictionaryWithObject:[NSNumber numberWithInt:signal] forKey:UncaughtExceptionHandlerSignalKey];
  
  NSArray *callStack = [DebugUtil backTrace];
  
  [userInfo setObject:callStack forKey:UncaughtExceptionHandlerAddressesKey];
  
  [[[DebugUtil alloc] init] performSelectorOnMainThread:@selector(handleException:) withObject: [NSException exceptionWithName:UncaughtExceptionHandlerSignalExceptionName reason:[NSString stringWithFormat: NSLocalizedString(@"Signal %d was raised.\n" @"%@", nil), signal, getAppInfo()] userInfo:userInfo] waitUntilDone:YES];
}
{% endcodeblock %}

第4部分 原文：http://www.cocoachina.com/newbie/tutorial/2012/0829/4672.html
