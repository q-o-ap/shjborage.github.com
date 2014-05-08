---
layout: post
title: "单例初始化代码中,一定不能出现对单例的引用"
date: 2014-04-17 23:03
comments: true
categories: iOS
tags: iOS 设计模式
---

写了个测试的例子：

一个单例类 SingletonClass 和 这个单例中用到的类 SomeClass

**SingletonClass**

```
@implementation SingletonClass

+ (id)sharedSingletonClass
{
    static SingletonClass* _singleton = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        _singleton = [[self alloc] init];
    });
    return _singleton;
}

- (id)init
{
    self = [super init];
    if (self)
    {
        self.number = 10;//一个属性
        self.someClass = [[SomeClass alloc] init];//初始化SomeClass
    }
    return self;
    
}

@end
```

**SomeClass：**

```
@implementation SomeClass

- (id)init
{
    self = [super init];
    if (self)
    {
        _number = [[SingletonClass sharedSingletonClass] number];//此处用到了单例中的number的值
    }
    return self;
}

@end
```

当运行的时候发现
```
    NSLog(@"Hello, World!");
    NSLog(@"%@:", [SingletonClass sharedSingletonClass])
	NSLog(@"Hello, World!")
```
        
第二个helloworld永远执行不到

原因很简单，循环调用了，在单例初始化过程中，又调用了单例的初始化，以致于永远跳出不这个死循环。用dispatch_once的单例和之前常用的单例生成方法在这里没有区另。只是目测dispatch_once在发现被"dispatch_twice"之后就静止不动了，也不报错。之前的单例生成方法会执行死循环，追踪了下发现函数调用N多，一直卡着，但估计栈不久就会爆掉。  

**总结**：*这问题这样写着一目了然，等类多了而且结构复杂了就不太能看出来了，而且debug很是费解，以后得留点心*

**在单例初始化时（包括初始化函数中深度遍历的所有的代码），一定不能出现对单例的引用。**

引自:<http://www.cnblogs.com/sunnyxx/archive/2012/11/09/2762861.html>