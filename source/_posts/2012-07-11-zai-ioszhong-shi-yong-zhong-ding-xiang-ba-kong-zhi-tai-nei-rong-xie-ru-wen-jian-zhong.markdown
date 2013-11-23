---
layout: post
title: "在iOS中使用重定向，把控制台内容写入文件中"
date: 2012-07-11 17:39
comments: true
categories: iOS
---

转自：http://mmz06.blog.163.com/blog/static/121416962011111210462288/
###引入
在做j2ee开发的时候，会用到log4j或其它类似工具去记录程序运行日志，以便调试时或在程序运行出错后，通过日志查询出错原因。那在IOS开发中是否有类似的方式来记录日志呢？下面将简单的说明：
 
###说明
在Objective-c开发程序的时候，有专门的日志操作类NSLog，它将指定的输出，输出到（stderr）,我们可以利用Xcode的日志输出窗口，那么既然是要记录到具体日志文件，我们就想输出日志写入到具体的日志文件即可。

<!-- more -->
 
###1、  宏定义（下面是我在程序中常用到的日志宏，用DEBUG开关管理，
也就是说只有在DEBUG模式下才让日志输出 ：）

``` 
#ifdef DEBUG 
#  define LOG(fmt, ...) do {                                            \ 
        NSString* file = [[NSString alloc] initWithFormat:@"%s", __FILE__]; \ 
        NSLog((@"%@(%d) " fmt), [file lastPathComponent], __LINE__, ##__VA_ARGS__); \ 
        [file release];                                                 \ 
    } while(0) 
#  define LOG_METHOD NSLog(@"%s", __func__) 
#  define LOG_CMETHOD NSLog(@"%@/%@", NSStringFromClass([self class]), NSStringFromSelector(_cmd)) 
#  define COUNT(p) NSLog(@"%s(%d): count = %d\n", __func__, __LINE__, [p retainCount]); 
#  define LOG_TRACE(x) do {printf x; putchar('\n'); fflush(stdout);} while (0) 
#else 
#  define LOG(...) 
#  define LOG_METHOD 
#  define LOG_CMETHOD 
#  define COUNT(p) 
#  define LOG_TRACE(x) 
#endif
```
 
可以看到，除了标准的用户定义输出外，我还加入了许多有用的信息，
比如源程序文件位置，行号，类名，函数名等。具体的应用可以在具体的开发过程中添加、删除。
 
###2、  应用：

{% codeblock %}
- (void)redirectNSLogToDocumentFolder{ 
    NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory,NSUserDomainMask, YES); 
    NSString *documentsDirectory = [paths objectAtIndex:0]; 
    NSString *fileName =[NSString stringWithFormat:@"%@.log",[NSDate date]]; 
    NSString *logFilePath = [documentsDirectory stringByAppendingPathComponent:fileName]; 
    freopen([logFilePath cStringUsingEncoding:NSASCIIStringEncoding],"a+",stderr); 
} 
 
- (void)applicationDidFinishLaunching:(UIApplication *)application { 
    // 真机测试时保存日志 
    if ([CDeviceInfo getModelType] != SIMULATOR) { 
        [self redirectNSLogToDocumentFolder]; 
} 
}
```
{% endcodeblock %}
 
真机测试的时候，可以利用freopen将标准错误输出保存到指定的文件当中，这样就可以在问题发生后分析日志文件。
 
###3、  设置DEBUG标志是否正确定义
 
Xcode 一般会在 debug 运行配置项里面已经定义号了DEBUG 标志，如果没定义我们就自己写上，以我的 Xcode 4 为例，在项目get Info中找到 PreProcessor Macros 这个属性，对于 Debug 配置我们给他写上 DEBUG，而在 Release 配置中把它留空。 这样我们刚才那段预处理命令就可以根据这个标志来判断我们编译的时调试版本还是发布版本，从而控制 NSLog 的输出。 (因为 Xcode 4 会把 debug/release 两个配置项同时对比展现出来，而 3.x 版本的只能分别设置，如果你用的时xcode 3.x 开发工具， 那么就分别对 Debug/Release 都检查一下)。