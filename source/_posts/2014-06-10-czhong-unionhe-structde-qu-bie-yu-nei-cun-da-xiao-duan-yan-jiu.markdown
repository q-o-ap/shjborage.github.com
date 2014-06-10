---
layout: post
title: "C中Union和Struct的区别与内存大小端研究"
date: 2014-06-10 15:51
comments: true
categories: C
tags: C 底层
---

转自<http://blog.csdn.net/lvyexiaozi/article/details/2621694>

首先看看union，在c++中，union可能没有多大用处，
在c语言中，可能我们要借助其完成很多巧妙的设计，下面是其一个完整的定义：

```
union UTest {              
	double dlOne;              
	char   chOne;              
	byte   bArray[4];         
	};
```

好了，看到上面的定义，很像struct的定义，但是对于union来说，有几点是值得注意的：

+	不能直接对其进行初始化；
+	某个时候只能使用其中的一个元素；
+	最后一点也是最重要的一点就是内存共享，分配给`union`内存的`Size`是其中`Size`最大的那个元素的`Size`。

说到这里，既然union最重要的是内存共享，那么我们做如下定义：`union UTest tEle;`然后赋值：`tEle.dlOne = 2.0f;`现在是`dlOne`可用，下一步：`tEle.chOne = 'A'；`到这里`dlOne`失去了其意义，`chOne`变得可用。


然后，我们再来看看Struct，在struct中每一个元素都是分配内存的，而且都是有单独意义的，也就是说对一个变量的赋值并不影响其它变量的取值。到这里，各位应该明白这两者之间的区别了吧，事实上我个人认为，它们最主要的区别是在内存的分配和使用上。知道这一点，一切也就不难理解了。

最后，在使用union的时候，可能有时候我们会来用其来对字节流进行分解和重组，这样使用的时候一定要注意各种内存对数据的存储，比如Intel是按高高低低的原则存储的，有些则是相反的。因此，这点因该值得注意，否则得到的可能和预期的结果不一样。


##举例：使用union结构输出主机字节序

原理解析：使用short来存储数字，因为共享内存空间，使用char数组来看存储在顺序，来分析是大端还是小端。

```
// if need...
#define CPU_VENDOR_OS "MAC-X64"

int main (void)
{
  union {
    short s;
    char c[sizeof(short)];
  } un;
  
  un.s = 0x0A0B;
  
  printf ( "%s:", CPU_VENDOR_OS);
  
  if ( 2==sizeof(short)) {
    if ( 0x0A==un.c[0] && 0x0B==un.c[1]) {
      printf ( "big-endian\n" );
    } else if ( 0x0B==un.c[0] && 0x0A==un.c[1] ) {
      printf ( "little-endian\n" );
    } else {
      printf ( "unknown\n" );
    }
  } else {
    printf ("sizeof(short) = %d\n", sizeof(short));
  }
  return 0;
}
```