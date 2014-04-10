---
layout: post
title: "GCD的坑"
date: 2014-02-19 22:22
comments: true
categories: iOS
---

最近用不少GCD的东西，相信很多童鞋也会被GCD的简便给吸引过来。
但是这玩意使用起来，还是有不少的坑，列举出来，希望能对遇到的有用。

<!-- more -->

大多数时间没啥事，但是有时这样使用，就会带来问题了。见测试代码：

```
//  dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
  
    for (int i=0; i<=100; i++) {
//    for (int i=0; i<=62; i++) {
      dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        NSString *key = [NSString stringWithFormat:@"key%d", i];
        NSMutableDictionary *value = [NSMutableDictionary dictionary];
        for (int j=0; j<1/*10000*/; j++) {
          NSString *subKey = [NSString stringWithFormat:@"subkey%d", j];
          int random = arc4random();
          [value setObject:[NSString stringWithFormat:@"%d", random] forKey:subKey];
        }
        
//        int j=100;
//        while (--j>0) {
//          dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
//            NSLog(@"sub");
//          });
//        }
        
//        [[TMCache sharedCache] setObject:value forKey:key];
        
        [[TMCache sharedCache] setObject:value forKey:key block:NULL];
        
//        TMDiskCache *cache = [[TMDiskCache alloc] initWithName:@"test.diskCache"];
//        [cache setObject:value forKey:key block:NULL];
        
        NSLog(@"%@", key);
      });
    }
  
//  });
```

*TMCache setObject:forKey:*

```
- (void)setObject:(id <NSCoding>)object forKey:(NSString *)key
{
    if (!object || !key)
        return;
    
    dispatch_semaphore_t semaphore = dispatch_semaphore_create(0);

    [self setObject:object forKey:key block:^(TMCache *cache, NSString *key, id object) {
        dispatch_semaphore_signal(semaphore);
    }];

    dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);

    #if !OS_OBJECT_USE_OBJC
    dispatch_release(semaphore);
    #endif
}
```

*TMDiskCache 定义*
```
//#if __IPHONE_OS_VERSION_MIN_REQUIRED >= __IPHONE_4_0 //这个是默认值
#if 0
    #define TMCacheStartBackgroundTask() UIBackgroundTaskIdentifier taskID = UIBackgroundTaskInvalid; \
            taskID = [[UIApplication sharedApplication] beginBackgroundTaskWithExpirationHandler:^{ \
            [[UIApplication sharedApplication] endBackgroundTask:taskID]; }];
    #define TMCacheEndBackgroundTask() [[UIApplication sharedApplication] endBackgroundTask:taskID];
#else
    #define TMCacheStartBackgroundTask()
    #define TMCacheEndBackgroundTask()
#endif
```

*TMDiskCache 使用*
```
- (void)setObject:(id <NSCoding>)object forKey:(NSString *)key block:(TMDiskCacheObjectBlock)block
{
    NSDate *now = [[NSDate alloc] init];

    if (!key || !object)
        return;

    TMCacheStartBackgroundTask();

    __weak TMDiskCache *weakSelf = self;

    dispatch_async(_queue, ^{
        TMDiskCache *strongSelf = weakSelf;
        if (!strongSelf) {
            TMCacheEndBackgroundTask();
            return;
        }

     	...
     
        TMCacheEndBackgroundTask();
    });
}
```

这样一个for循环，次数超过63就卡死了，啥也执行不了。
还有，如果关掉`TMCacheStartBackgroundTask`，并且不使用`dispatch_semaphore_t`做异步转同步的处理（直接使用`[[TMCache sharedCache] setObject:value forKey:key block:NULL];`），就没啥问题了。


看详情，跑数据的在此[下载测试工程](https://www.dropbox.com/s/59kdkfc3sm2gx1q/test_GCD.zip)
