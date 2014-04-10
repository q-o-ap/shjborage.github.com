---
layout: post
title: "SQLite(FMDB) 线程安全 - 多线程处理"
date: 2013-12-12 11:25
comments: true
categories: iOS Database
---

在iOS开发中，经常用到SQLite，CoreData也不错，但很多时候还是自己写sql，使用FMDB来处理。当然了，也可以不用FMDB，直接写处理，这个不解释。

但是，我们在数据库操作的时候，经常遇到多线程访问数据库的状况，常见的有几种处理方法：

*	1, NSLock或类似方法加锁
*	2, FMDatabaseQueue
*	3, SQLITE_CONFIG_SERIALIZED

下面我们来仔细看一下出现的问题以及相关的解决方案


<!-- more -->

###常见问题

以使用FMDB为例：

The debugger says that the error occurs at [FMResultSet next] at the line, has `EXC_BAD_ACCESS`

```
rc = sqlite3_step(statement.statement);
```

多线程访问的时候，会在这里报错。

如何解决呢？

####1. NSLock加锁（NSMutex）

详见苹果官方文档：
[Threading Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Multithreading/ThreadSafety/ThreadSafety.html)

####2. FMDatabaseQueue

这个是我在某项目中用到的，所有查询都走这里。
可能因为项目较复杂，用了这个还是会时候有问题，但稳定很多。可以有一些别的地方调用，没查清楚。

```
// 相关引用
_fmQueue = [FMDatabaseQueue databaseQueueWithPath:sqlitePath];

// 查询数据
- (FMResultSet *)executeQueryWithSQL:(NSString *)sql
{
  BFLogVerbose(@"executeQuery: %@", sql);
  
  __block FMResultSet *rs = nil;
  __block dispatch_semaphore_t sem = dispatch_semaphore_create(0);
  
  [_fmQueue inDatabase:^(FMDatabase *fmdb){
    FMResultSet *tmpRs = [fmdb executeQuery:sql];
    rs = [FMResultSet resultSetWithStatement:tmpRs.statement usingParentDatabase:fmdb];
    [tmpRs close];
    dispatch_semaphore_signal(sem);
  }];
  
  dispatch_semaphore_wait(sem, DISPATCH_TIME_FOREVER);
  dispatch_release(sem);
  
  return rs;
}
```

###3. SQLITE_CONFIG_SERIALIZED

SQLite provides a much simpler serialization. By just setting the sqlite_config() option SQLITE_CONFIG_SERIALIZED you will probably avoid most of these kinds of headaches. I discovered this the hard way after fighting with threading issues for a long while.


```
if (sqlite3_config(SQLITE_CONFIG_SERIALIZED) == SQLITE_ERROR) {
        NSLog(@"couldn't set serialized mode");
}
```


参考：
http://stackoverflow.com/questions/3144700/exc-bad-access-when-using-sqlite-fmdb-and-threads-on-ios-4-0