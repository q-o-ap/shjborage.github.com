---
layout: post
title: "C Objective-C 底层研究 常用方法"
date: 2014-05-08 10:15
comments: true
categories: C、C++
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

