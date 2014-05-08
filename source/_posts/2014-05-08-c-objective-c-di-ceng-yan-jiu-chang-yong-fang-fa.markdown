---
layout: post
title: "C Objective-C 底层研究 常用方法"
date: 2014-05-08 10:15
comments: true
categories: C iOS
---

经常会遇到一些高级宏不知道什么意思，再或者搞iOS的时候，ARC、Block不知道实现的原理，怎么办？
简单，看下文。

<!-- more -->

##预编译

*查看宏替换后的结果，看编译前的样子*

**`gcc -E xxx.c`**

源码:

```
#include <stdio.h>

#define MAX(x, y) (x) > (y) ? (x) : (y)

int main() {
  int a = 5, b = 7;
  int max = MAX(a, b);

  printf("max of a,b is %d\n", max);
  return 0;
}
```

==>

```
// 省略若干行，这里是stdio.h的头文件被包含进来了。。
extern int __vsnprintf_chk (char * restrict, size_t, int, size_t,
       const char * restrict, va_list);
# 491 "/usr/include/stdio.h" 2 3 4
# 2 "main.c" 2



int main() {
  int a = 5, b = 7;
  int max = (a) > (b) ? (a) : (b);

  printf("max of a,b is %d\n", max);
  return 0;
}
```

从上面转换完的代码可以发现，替换结果如此清晰。

与这个类似的，还有`Objective-C`中的处理 **`clang -E xxx.m`**. 当然还有`clang -S xx.m` 转换成汇编。。


## Objective-C代码转换为C代码

**`clang -rewrite-objc xxx.m`** 这个在研究Block等时很实用，直接上代码

```
#include <stdio.h>

int main() {
	const char *fmt = "val = %d\n";
	int val = 9;
	int tmp = 1024;
	
	void (^blk)() = ^{printf(fmt, val);};

	blk();
  
	return 0;
}

```

==>

```
// 此处省略453行

struct __main_block_impl_0 {
  struct __block_impl impl;
  struct __main_block_desc_0* Desc;
  const char *fmt;
  int val;
  __main_block_impl_0(void *fp, struct __main_block_desc_0 *desc, const char *_fmt, int _val, int flags=0) : fmt(_fmt), val(_val) {
    impl.isa = &_NSConcreteStackBlock;
    impl.Flags = flags;
    impl.FuncPtr = fp;
    Desc = desc;
  }
};
static void __main_block_func_0(struct __main_block_impl_0 *__cself) {
  const char *fmt = __cself->fmt; // bound by copy
  int val = __cself->val; // bound by copy
printf(fmt, val);}

static struct __main_block_desc_0 {
  size_t reserved;
  size_t Block_size;
} __main_block_desc_0_DATA = { 0, sizeof(struct __main_block_impl_0)};
int main() {
 const char *fmt = "val = %d\n";
 int val = 9;
 int tmp = 1024;

 void (*blk)() = (void (*)())&__main_block_impl_0((void *)__main_block_func_0, &__main_block_desc_0_DATA, fmt, val);

 ((void (*)(__block_impl *))((__block_impl *)blk)->FuncPtr)((__block_impl *)blk);

 return 0;
}
static struct IMAGE_INFO { unsigned version; unsigned flag; } _OBJC_IMAGE_INFO = { 0, 2 };
```

参考文献：Objective-C高级编程 iOS与OSX多线程和内存管理