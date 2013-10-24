---
layout: post
title: "遭遇SIGPIPE"
date: 2013-10-10 23:35
comments: true
categories: iOS
---
《转》

我写了一个服务器程序, 在Windows下在cygwin环境编译后执行, 
然后用C#写了多线程客户端进行压力测试. 程序一直运行正常. 
但当在Linux下测试时, 总是莫名退出. 最后跟踪到是write调用导致退出. 

用gdb执行程序, 退出时提示"Broken pipe".
最后问题确定为, 对一个对端已经关闭的socket调用两次write, 
第二次将会生成SIGPIPE信号, 该信号默认结束进程.

<!-- more -->

具体的分析可以结合TCP的"四次握手"关闭. TCP是全双工的信道, 
可以看作两条单工信道, TCP连接两端的两个端点各负责一条. 当对端调用close时, 
虽然本意是关闭整个两条信道, 但本端只是收到FIN包. 按照TCP协议的语义, 
表示对端只是关闭了其所负责的那一条单工信道, 仍然可以继续接收数据. 

也就是说, 因为TCP协议的限制, 
一个端点无法获知对端的socket是调用了close还是shutdown.
对一个已经收到FIN包的socket调用read方法, 如果接收缓冲已空, 则返回0, 
这就是常说的表示连接关闭. 但第一次对其调用write方法时, 如果发送缓冲没问题, 
会返回正确写入(发送). 但发送的报文会导致对端发送RST报文, 
因为对端的socket已经调用了close, 完全关闭, 既不发送, 也不接收数据. 所以, 
第二次调用write方法(假设在收到RST之后), 会生成SIGPIPE信号, 导致进程退出.

为了避免进程退出, 可以捕获SIGPIPE信号, 或者忽略它, 
给它设置SIG_IGN信号处理函数:
`signal(SIGPIPE, SIG_IGN);`
这样, 第二次调用write方法时, 会返回-1, 同时errno置为SIGPIPE. 
程序便能知道对端已经关闭.

PS: Linux下的SIGALRM似乎会每1秒钟往后偏移1毫秒, 但Windows下经过测试完全准时, 不差1毫秒.

#####忽略SIGPIPE信号的方法

http://hi.baidu.com/greathongjian/blog/item/2f695643091885139213c65a.html

```
struct sigaction sa;
sa.sa_handler = SIG_IGN;//设定接受到指定信号后的动作为忽略
sa.sa_flags = 0;
if (sigemptyset(&sa.sa_mask) == -1 || //初始化信号集为空
sigaction(SIGPIPE, &sa, 0) == -1) { //屏蔽SIGPIPE信号
perror("failed to ignore SIGPIPE; sigaction");
exit(EXIT_FAILURE);
}
```
#####pthread线程里如何屏蔽SIGPIPE异常

hi.baidu.com/ailacy/blog/item/a7eb65f8b8b55707d8f9fdd5.html
http://bbs2.chinaunix.net/viewthread.php?tid=985166&extra=&page=1

在pthread中，可能会遇到Program received signal SIGPIPE, Broken 
pipe的问题，解决方法是每一个线程启动之前时，先执行下面代码：

```
#ifndef WIN32
sigset_t signal_mask;
sigemptyset (&signal_mask);
sigaddset (&signal_mask, SIGPIPE);
int rc = pthread_sigmask (SIG_BLOCK, &signal_mask, NULL);
if (rc != 0) {
printf("block sigpipe error\n");
}
```