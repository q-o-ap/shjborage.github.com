---
layout: post
title: "redis-源码研究之-INCR"
date: 2016-12-28 16:47
comments: true
categories: Web Database other C
tags: redis 后端
---

调用类似微博的关注 Feed 流的实现时，发现早在2009年，[redis](https://redis.io) 官方就写过这样[一篇文章](https://redis.io/topics/twitter-clone)：通过 `redis` 的各种 API 来做数据存储，方便的实现 Push 的关注流。

简单研究了一下，最终改了一点点代码，详情见 [Github](https://github.com/shjborage/retwis)。

今天主要研究一下这个命令 [INCR](https://redis.io/commands/incr) ，看 `redis` 是如何实现这个命令在多线程访问的。

<!-- more -->

## 准备工作

1. `redis` 源码 <https://github.com/antirez/redis>
2. 当然还离不开官方的源码说明：<https://github.com/antirez/redis#redis-internals>
3. `vim` + `ctags` 自行脑补看代码的流程吧~

## main 入口
找到 `main` 函数，Server、Client 应该至少有两个：

```
find ./* | xargs grep -n 'main('

// 重要结果
./redis-cli.c:2552:int main(int argc, char **argv) {
./server.c:3566:int main(int argc, char **argv) {
```

发现 `server.c`，在 `server.h` 中找到各种结构体的说明（官网也有讲）

### server.h

```
struct redisServer {
    /* General */
    pid_t pid;                  /* Main process pid. */
    char *configfile;           /* Absolute config file path, or NULL */
    char *executable;           /* Absolute executable file path. */
    char **exec_argv;           /* Executable argv vector (copy). */
    int hz;                     /* serverCron() calls frequency in hertz */
    redisDb *db;
    dict *commands;             /* Command table */
    ...
    list *clients;              /* List of active clients */
    ...
    client *master;     /* Client that is master for this slave */
    ...
}
```

```
typedef struct client {
    int fd;
    sds querybuf;
    int argc;
    robj **argv;
    redisDb *db;
    int flags;
    list *reply;
    char buf[PROTO_REPLY_CHUNK_BYTES];
    ... many other fields ...
} client;
```

```
typedef struct redisObject {
    unsigned type:4;
    unsigned encoding:4;
    unsigned lru:LRU_BITS; /* LRU time (relative to server.lruclock) or
                            * LFU data (least significant 8 bits frequency
                            * and most significant 16 bits decreas time). */
    int refcount;
    void *ptr;
} robj;
```

### server.c

重要初始化流程：

```
initServerConfig() //setups the default values of the server structure.
initServer() //allocates the data structures needed to operate, setup the listening socket, and so forth.
aeMain() // starts the event loop which listens for new connections.
```

`call()` is used in order to call a given command in the context of a given client.

这个就是我们要找的处理命令入口方法了。

附上一个完整的 `Command` 说明:

> The global variable `redisCommandTable` defines all the Redis commands, specifying the name of the command, the function implementing the command, the number of arguments required, and other properties of each command.
 
```
struct redisCommand redisCommandTable[] = {
    {"module",moduleCommand,-2,"as",0,NULL,1,1,1,0,0},
    {"get",getCommand,2,"rF",0,NULL,1,1,1,0,0},
    {"set",setCommand,-3,"wm",0,NULL,1,1,1,0,0},
    ...
    {"incr",incrCommand,2,"wmF",0,NULL,1,1,1,0,0},
    {"decr",decrCommand,2,"wmF",0,NULL,1,1,1,0,0},
    ...
```

但我们发现， `call()` 是由 `processCommand()` 调用的，`processCommand()` 的调用在 `server.c` 中并没有。 还需努力。。。 

继续看官方文档（其实也在本地搜索了一下）

```
find ./* | xargs grep -n 'processCommand'

// 结果
./cluster.c:5128:                 * not trapped earlier in processCommand(). Report the same
grep: ./modules: Is a directory
./networking.c:1299:            if (processCommand(c) == C_OK)
Binary file ./networking.o matches
Binary file ./redis-check-rdb matches
Binary file ./redis-sentinel matches
Binary file ./redis-server matches
./server.c:2226: * processCommand() execute the command or prepare the
./server.c:2232:int processCommand(client *c) {
./server.h:1547:int processCommand(client *c);
Binary file ./server.o matches
./tags:3304:processCommand	server.c	/^int processCommand(client *c) {$/;"	f
```

`server.h` 中已经暴露出了 API，提供给了 `networking.c`。所以继续下文。

## networking.c


> `createClient()` // allocates and initializes a new client.
> the `addReply*()` family of functions are used by commands implementations in order to append data to the client structure, that will be transmitted to the client as a reply for a given command executed.
> `writeToClient()` transmits the data pending in the output buffers to the client and is called by the writable event handler sendReplyToClient().
> `readQueryFromClient()` is the readable event handler and accumulates data from read from the client into the query buffer.
> `processInputBuffer()` is the entry point in order to parse the client query buffer according to the Redis protocol. Once commands are ready to be processed, it calls `processCommand()` which is defined inside server.c in order to actually execute the command.
> `freeClient()` deallocates, disconnects and removes a client.


基本上理完了整体的调用流程：

![](http://shjborage-public.qiniudn.com/2016-12-28-14824009373678.jpg)


其它细节待研究，我们重点看 `call()`。

## call()

研究了一下 `callgraph`，把 C 代码的调用关系梳理了一下（比干巴巴的代码好些，当然还要结合代码去分析）：

```
callgraph -b chrome -f call
```

![](http://shjborage-public.qiniudn.com/2016-12-28-14824095565432.jpg)

找到 **`proc`** 这个重点函数

```
 2127     /* Call the command. */
 2128     dirty = server.dirty;
 2129     start = ustime();
 2130     c->cmd->proc(c);
 2131     duration = ustime()-start;
 2132     dirty = server.dirty-dirty;
 2133     if (dirty < 0) dirty = 0;
```

到 `client` 中了，需要看下这个 `cmd`（`redisCommand`） 下的 `proc` (`redisCommandProc`)

```
typedef void redisCommandProc(client *c);
```

需要找出这个 `callback` 在哪设置的，如何执行。

我们的 `client` 里面主要是从客户端那拿解析到的命令，是通过 `argc` 和 `argv` 来表现的。
在 `processCommand` 中通过 `lookupCommand` 来给 `cmd` 赋值，在 `server` 中有 `commands` 保存的命令列表。

```
2243     /* Now lookup the command and check ASAP about trivial error conditions
2244      * such as wrong arity, bad command name and so forth. */
2245     c->cmd = c->lastcmd = lookupCommand(c->argv[0]->ptr);
```

`commands` 是在 `initServerConfig` 中初始化的，也是根据 `redisCommandTable` 进行的。

```
{"incr",incrCommand,2,"wmF",0,NULL,1,1,1,0,0},
```

如下代码进行了初始化：

```
int numcommands = sizeof(redisCommandTable)/sizeof(struct redisCommand);

for (j = 0; j < numcommands; j++) {
    struct redisCommand *c = redisCommandTable+j;
    char *f = c->sflags;
    ...
}
```

redisCommand 定义：
```
 1161 struct redisCommand {
 1162     char *name;
 1163     redisCommandProc *proc;
 1164     int arity;
 1165     char *sflags; /* Flags as string representation, one char per flag. */
 1166     int flags;    /* The actual flags, obtained from the 'sflags' field. */
 1167     /* Use a function to determine keys arguments in a command line.
 1168      * Used for Redis Cluster redirect. */
 1169     redisGetKeysProc *getkeys_proc;
 1170     /* What keys should be loaded in background when calling this command? */
 1171     int firstkey; /* The first argument that's a key (0 = no keys) */
 1172     int lastkey;  /* The last argument that's a key */
 1173     int keystep;  /* The step between first and last key */
 1174     long long microseconds, calls;
 1175 };
```

所以每个命令都对应的 `proc`，就在 `redisCommandTable` 中。
`incr` 对应的就是 `incrCommand`，在 `t_string.c` 中又中转到 `incrDecrCommand`。

## incrDecrCommand(client *c, long long incr)

调用关系：

```
void incrCommand(client *c) {
    incrDecrCommand(c,1);
}
```

![](http://shjborage-public.qiniudn.com/2016-12-28-14828945195167.jpg)

先来分析两个参数：

```
client *c       // 客户端实例
long long incr  // 操作数， incr命令时为 `1`
```

看来半天，这里的结构也是基本的操作，除了 `server.dirty` 的操作外，貌似没有多线程相关的处理。

```
 341 void incrDecrCommand(client *c, long long incr) {
 342     long long value, oldvalue;
 343     robj *o, *new;
 344
 345     o = lookupKeyWrite(c->db,c->argv[1]);
 346     if (o != NULL && checkType(c,o,OBJ_STRING)) return;
 347     if (getLongLongFromObjectOrReply(c,o,&value,NULL) != C_OK) return;
 348
 349     oldvalue = value;
 350     if ((incr < 0 && oldvalue < 0 && incr < (LLONG_MIN-oldvalue)) ||
 351         (incr > 0 && oldvalue > 0 && incr > (LLONG_MAX-oldvalue))) {
 352         addReplyError(c,"increment or decrement would overflow");
 353         return;
 354     }
 355     value += incr;
 356
 357     if (o && o->refcount == 1 && o->encoding == OBJ_ENCODING_INT &&
 358         (value < 0 || value >= OBJ_SHARED_INTEGERS) &&
 359         value >= LONG_MIN && value <= LONG_MAX)
 360     {
 361         new = o;
 362         o->ptr = (void*)((long)value);
 363     } else {
 364         new = createStringObjectFromLongLong(value);
 365         if (o) {
 366             dbOverwrite(c->db,c->argv[1],new);
 367         } else {
 368             dbAdd(c->db,c->argv[1],new);
 369         }
 370     }
 371     signalModifiedKey(c->db,c->argv[1]);
 372     notifyKeyspaceEvent(NOTIFY_STRING,"incrby",c->argv[1],c->db->id);
 373     server.dirty++;
 374     addReply(c,shared.colon);
 375     addReply(c,new);
 376     addReply(c,shared.crlf);
 377 }
```

还是得回头看 `call()` 里面到底还有哪些多线程相关操作。

```
 2127     /* Call the command. */
 2128     dirty = server.dirty;
 2129     start = ustime();
 2130     c->cmd->proc(c);
 2131     duration = ustime()-start;
 2132     dirty = server.dirty-dirty;
 2133     if (dirty < 0) dirty = 0;

 ...

 2169         /* Check if the command operated changes in the data set. If so
 2170          * set for replication / AOF propagation. */
 2171         if (dirty) propagate_flags |= (PROPAGATE_AOF|PROPAGATE_REPL);
```

再来看看这个 `PROPAGATE_AOF` 是搞啥的

## aof.c

官方说明： <https://github.com/antirez/redis#aofc-and-rdbc>

> As you can guess from the names these files implement the RDB and AOF persistence for Redis. Redis uses a persistence model based on the fork() system call in order to create a thread with the same (shared) memory content of the main Redis thread. This secondary thread dumps the content of the memory on disk. This is used by rdb.c to create the snapshots on disk and by aof.c in order to perform the AOF rewrite when the append only file gets too big.

> The implementation inside aof.c has additional functions in order to implement an API that allows commands to append new commands into the AOF file as clients execute them.

> The call() function defined inside server.c is responsible to call the functions that in turn will write the commands into the AOF.
 
这个应该是主要原因了，一个通用的方案，因为时间关系，有时间再把这部分详细研究吧。

## Refs

[源码分析：静态分析 C 程序函数调用关系图](http://www.tinylab.org/callgraph-draw-the-calltree-of-c-functions/?utm_source=tuicool&utm_medium=referral)  
[DOT语言](https://zh.wikipedia.org/wiki/DOT%E8%AF%AD%E8%A8%80)

## Evernote

[redis-结构-源码研究备忘](https://www.evernote.com/l/AC5QDjLdH0ZN1K1zh9XY343jZaP1AWeYbS8)  
<https://www.evernote.com/l/AC4hEMNhKUFMnZ-_t3tLAHM4W2IbQBXSyeM>  



