---
layout: post
title: "深入浅出 Cocoa 多线程编程之 block 与 dispatch quene"
date: 2013-11-12 22:20
comments: true
categories: iOS
---

深入浅出 Cocoa 多线程编程之 block 与 dispatch quene
罗朝辉(http://blog.csdn.net/kesalin)
CC 许可，转载请注明出处

block 是 Apple 在 GCC 4.2 中扩充的新语法特性，其目的是支持多核并行编程。我们可以将 
dispatch_queue 与 block 结合起来使用，方便进行多线程编程。

<!-- more -->

本文源代码下载：[点击下载](http://www.cppblog.com/Files/kesalin/StudyBlocks.zip)

###1，实验工程准备

在 XCode 4.0 中，我们建立一个 Mac OS X Application 类型的 Command Line Tool，在 Type 里面我们选择 Foundation 就好，工程名字暂且为 StudyBlocks.默认生成的工程代码 main.m 内容如下：

{% codeblock %}
int main (int argc, const char * argv[])  
{  
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];  
  
    // insert code here...  
    NSLog(@"Hello, World!");  
  
    [pool drain];  
    return 0;  
}  
{% endcodeblock %}

###2，如何编写 block
在自动生成的工程代码中，默认打印一条语句"Hello, World!"，这个任务可以不可以用 block 语法来实现呢？答案是肯定的，请看：

```
void (^aBlock)(void) = ^(void){ NSLog(@"Hello, World!"); };  
aBlock();  
```

用上面的这两行语句替换 main.m 中的 NSLog(@"Hello, World!"); 语句，编译运行，结果是一样的。  


这两行语句是什么意思呢？首先，等号左边的 void (^aBlock)(void) 表示声明了一个 block，这个 block 不带参数(void)且也无返回参数(void)；等号右边的 ^(void){ } 结构表示一个 block 的实现体，至于这个 block 具体要做的事情就都在 {} 之间了。在这里我们仅仅是打印一条语句。整个语句就是声明一个 block，并对其赋值。第二个语句就是调用这个 block 做实际的事情，就像我们调用函数一样。block 很有点像 C++0X 中的 Lambda 表达式。

我们也可以这么写：

```
void (^aBlock)(void) = 0;  
aBlock = ^(void){  
    NSLog(@" >> Hello, World!");  
};  
aBlock();  
```

现在我们知道了一个 block 该如何编写了，那么 block 数组呢？也很简单，请看：

```
void (^blocks[2])(void) = {  
    ^(void){ NSLog(@" >> This is block 1!"); },  
    ^(void){ NSLog(@" >> This is block 2!"); }  
};
blocks[0]();  
blocks[1]();  
```

**谨记！**

block 是分配在 *stack* 上的，这意味着我们必须小心里处理 block 的生命周期。
比如如下的做法是不对的，因为 stack 分配的 block 在 if 或 else 内是有效的，但是到大括号 } 退出时就可能无效了：

{% codeblock %}
dispatch_block_t block;  
  
if (x) {  
    block = ^{ printf("true\n"); };  
} else {  
    block = ^{ printf("false\n"); };  
}  
block(); 
{% endcodeblock %}

上面的代码就相当于下面这样的 unsafe 代码：

```
if (x) {  
    struct Block __tmp_1 = ...; // setup details  
    block = &__tmp_1;  
} else {  
    struct Block __tmp_2 = ...; // setup details  
    block = &__tmp_2;  
}
```


###3，如何在 block 中修改外部变量
考虑到 block 的目的是为了支持并行编程，对于普通的 local 变量，我们就不能在 block 里面随意修改（原因很简单，block 可以被多个线程并行运行，会有问题的），而且如果你在 block 中修改普通的 local 变量，编译器也会报错。那么该如何修改外部变量呢？有两种办法，第一种是可以修改 static 全局变量；第二种是可以修改用新关键字 __block 修饰的变量。请看：

{% codeblock %}
__block int blockLocal  = 100;  
static int staticLocal  = 100;  
  
void (^aBlock)(void) = ^(void){   
    NSLog(@" >> Sum: %d\n", global + staticLocal);  
      
    global++;  
    blockLocal++;  
    staticLocal++;  
};  
  
aBlock();  
  
NSLog(@"After modified, global: %d, block local: %d, static local: %d\n", global, blockLocal, staticLocal);  
{% endcodeblock %}

相似的情况，我们也可以引用 static block 或 __block block。比如我们可以用他们来实现 block 递归：

{% codeblock %}
// 1  
void (^aBlock)(int) = 0;  
static void (^ const staticBlock)(int) = ^(int i) {  
    if (i > 0) {  
        NSLog(@" >> static %d", i);  
        staticBlock(i - 1);  
    }  
};  
  
aBlock = staticBlock;  
aBlock(5);  
  
// 2  
__block void (^blockBlock)(int);  
blockBlock = ^(int i) {  
    if (i > 0) {  
        NSLog(@" >> block %d", i);  
        blockBlock(i - 1);  
    }  
};  
  
blockBlock(5);  
{% endcodeblock %}

###4，上面我们介绍了 block 及其基本用法，但还没有涉及并行编程。 block 与 Dispatch Queue 分发队列结合起来使用，是 iOS 中并行编程的利器。请看代码：

{% codeblock %}
NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];  
  
initData();  
  
// create dispatch queue  
//  
dispatch_queue_t queue = dispatch_queue_create("StudyBlocks", NULL);  
  
dispatch_async(queue, ^(void) {  
    int sum = 0;  
    for(int i = 0; i < Length; i++)  
        sum += data[i];  
      
    NSLog(@" >> Sum: %d", sum);  
      
    flag = YES;  
});  
  
// wait util work is done.  
//  
while (!flag);  
dispatch_release(queue);  
  
[pool drain];  
{% endcodeblock %}

上面的 block 仅仅是将数组求和。  

首先，我们创建一个串行分发队列，然后将一个 block 任务加入到其中并行运行，这样 block 就会在新的线程中运行，直到结束返回主线程。在这里要注意 flag 的使用。flag 是 static 的，所以我们可以 block 中修改它。 语句 while (!flag); 的目的是保证主线程不会 blcok 所在线程之前结束。

dispatch_queue_t 的定义如下：
typedef void (^dispatch_block_t)( void);
这意味着加入 dispatch_queue 中的 block 必须是无参数也无返回值的。

dispatch_queue_create 的定义如下： 
 
```
dispatch_queue_t dispatch_queue_create(const char *label, dispatch_queue_attr_t attr);
```

这个函数带有两个参数：一个用于标识 dispatch_queue 的字符串；一个是保留的 dispatch_queue 属性，将其设置为 NULL 即可。

我们也可以使用

```
dispatch_queue_t dispatch_get_global_queue(long priority, unsigned long flags);
```

来获得全局的 dispatch_queue，参数 priority 表示优先级，值得注意的是：我们不能修改该函数返回的 dispatch_queue。

dispatch_async 函数的定义如下：

```
void dispatch_async(dispatch_queue_t queue, dispatch_block_t block);
```

它是将一个 block 加入一个 dispatch_queue，这个 block 会再其后得到调度时，并行运行。
相应的 dispatch_sync 函数就是同步执行了，一般很少用到。比如上面的代码如果我们修改为 dispatch_sync，那么就无需编写 flag 同步代码了。

###5，dispatch_queue 的运作机制及线程间同步
我们可以将许多 blocks 用 dispatch_async 函数提交到到 dispatch_queue 串行运行。这些 blocks 是按照 FIFO(先入先出)规则调度的，也就是说，先加入的先执行，后加入的一定后执行，但在某一个时刻，可能有多个 block 同时在执行。

在上面的例子中，我们的主线程一直在轮询 flag 以便知晓 block 线程是否执行完毕，这样做的效率是很低的，严重浪费 CPU 资源。我们可以使用一些通信机制来解决这个问题，如：semaphore（信号量）。 semaphore 的原理很简单，就是生产-消费模式，必须生产一些资源才能消费，没有资源的时候，那我就啥也不干，直到资源就绪。下面来看代码：

{% codeblock %}
NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];  
  
initData();  
  
// Create a semaphore with 0 resource  
//  
__block dispatch_semaphore_t sem = dispatch_semaphore_create(0);  
  
// create dispatch semaphore  
//  
dispatch_queue_t queue = dispatch_queue_create("StudyBlocks", NULL);  
  
dispatch_async(queue, ^(void) {  
    int sum = 0;  
    for(int i = 0; i < Length; i++)  
        sum += data[i];  
      
    NSLog(@" >> Sum: %d", sum);  
      
    // signal the semaphore: add 1 resource  
    //  
    dispatch_semaphore_signal(sem);  
});  
  
// wait for the semaphore: wait until resource is ready.  
//  
dispatch_semaphore_wait(sem, DISPATCH_TIME_FOREVER);  
  
dispatch_release(sem);  
dispatch_release(queue);  
  
[pool drain];  
{% endcodeblock %}

首先我们创建一个 `__block semaphore`，并将其资源初始值设置为 0 (不能少于 0)，在这里表示任务还没有完成，没有资源可用主线程不要做事情。然后在 block 任务完成之后，使用 dispatch_semaphore_signal 增加 semaphore 计数（可理解为资源数），表明任务完成，有资源可用主线程可以做事情了。而主线程中的 dispatch_semaphore_wait 就是减少 semaphore 的计数，如果资源数少于 0，则表明资源还可不得，我得按照FIFO（先等先得）的规则等待资源就绪，一旦资源就绪并且得到调度了，我再执行。

###6 示例：
下面我们来看一个按照 FIFO 顺序执行并用 semaphore 同步的例子：先将数组求和再依次减去数组。

{% codeblock %}
NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];  
  
initData();  
  
__block int sum = 0;  
  
// Create a semaphore with 0 resource  
//  
__block dispatch_semaphore_t sem = dispatch_semaphore_create(0);  
__block dispatch_semaphore_t taskSem = dispatch_semaphore_create(0);  
  
// create dispatch semaphore  
//  
dispatch_queue_t queue = dispatch_queue_create("StudyBlocks", NULL);  
  
dispatch_block_t task1 = ^(void) {  
    int s = 0;  
    for (int i = 0; i < Length; i++)  
        s += data[i];  
    sum = s;  
      
    NSLog(@" >> after add: %d", sum);  
  
    dispatch_semaphore_signal(taskSem);  
};  
  
dispatch_block_t task2 = ^(void) {  
    dispatch_semaphore_wait(taskSem, DISPATCH_TIME_FOREVER);  
      
    int s = sum;  
    for (int i = 0; i < Length; i++)  
        s -= data[i];  
    sum = s;  
  
    NSLog(@" >> after subtract: %d", sum);  
    dispatch_semaphore_signal(sem);  
};  
  
dispatch_async(queue, task1);  
dispatch_async(queue, task2);  
  
// wait for the semaphore: wait until resource is ready.  
//  
dispatch_semaphore_wait(sem, DISPATCH_TIME_FOREVER);  
  
dispatch_release(taskSem);  
dispatch_release(sem);  
dispatch_release(queue);  
  
[pool drain];  
{% endcodeblock %}

在上面的代码中，我们利用了 dispatch_queue 的 FIFO 特性，确保 task1 先于 task2 执行，而 task2 必须等待直到 task1 执行完毕才开始干正事，主线程又必须等待 task2 才能干正事。 这样我们就可以保证先求和，再相减，然后再让主线程运行结束这个顺序。

###7，使用 dispatch_apply 进行并发迭代：
对于上面的求和操作，我们也可以使用 dispatch_apply 来简化代码的编写：

{% codeblock %}
NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];  
  
initData();  
  
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);  
  
__block int sum = 0;  
__block int *pArray = data;  
  
// iterations  
//  
dispatch_apply(Length, queue, ^(size_t i) {  
    sum += pArray[i];  
});  
  
NSLog(@" >> sum: %d", sum);  
  
dispatch_release(queue);  
  
[pool drain];  
{% endcodeblock %}

注意这里使用了全局 dispatch_queue。

dispatch_apply 的定义如下：

```
dispatch_apply(size_t iterations, dispatch_queue_t queue, void (^block)(size_t));
```

参数 iterations 表示迭代的次数，void (^block)(size_t) 是 block 循环体。这么做与 for 循环相比有什么好处呢？答案是：并行，这里的求和是并行的，并不是按照顺序依次执行求和的。

###8, dispatch group
我们可以将完成一组相关任务的 block 添加到一个 dispatch group 中去，这样可以在 group 中所有 block 任务都完成之后，再做其他事情。比如 6 中的示例也可以使用 dispatch group 实现：

{% codeblock %}
NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];  
  
initData();  
  
__block int sum = 0;  
  
// Create a semaphore with 0 resource  
//  
__block dispatch_semaphore_t taskSem = dispatch_semaphore_create(0);  
  
// create dispatch semaphore  
//  
dispatch_queue_t queue = dispatch_queue_create("StudyBlocks", NULL);  
dispatch_group_t group = dispatch_group_create();  
  
dispatch_block_t task1 = ^(void) {  
    int s = 0;  
    for (int i = 0; i < Length; i++)  
        s += data[i];  
    sum = s;  
      
    NSLog(@" >> after add: %d", sum);  
      
    dispatch_semaphore_signal(taskSem);  
};  
  
dispatch_block_t task2 = ^(void) {  
    dispatch_semaphore_wait(taskSem, DISPATCH_TIME_FOREVER);  
      
    int s = sum;  
    for (int i = 0; i < Length; i++)  
        s -= data[i];  
    sum = s;  
      
    NSLog(@" >> after subtract: %d", sum);  
};  
  
// Fork  
dispatch_group_async(group, queue, task1);  
dispatch_group_async(group, queue, task2);  
  
// Join  
dispatch_group_wait(group, DISPATCH_TIME_FOREVER);  
  
dispatch_release(taskSem);  
dispatch_release(queue);  
dispatch_release(group);  
  
[pool drain];  
{% endcodeblock %}

在上面的代码中，我们使用 dispatch_group_create 创建一个 dispatch_group_t，然后使用语句：dispatch_group_async(group, queue, task1); 将 block 任务加入队列中，并与组关联，这样我们就可以使用 dispatch_group_wait(group, DISPATCH_TIME_FOREVER); 来等待组中所有的 block 任务完成再继续执行。

至此我们了解了 dispatch queue 以及 block 并行编程相关基本知识，开始在项目中运用它们吧，

*参考资料：*
Concurrency Programming Guide：
http://developer.apple.com/library/ios/#documentation/General/Conceptual/ConcurrencyProgrammingGuide/Introduction/Introduction.html
