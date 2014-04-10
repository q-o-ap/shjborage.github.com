---
layout: post
title: "二分查找最大的坑"
date: 2014-03-06 00:35
comments: true
categories: Interview
---

二分法大家都不陌生，每当找工作面试都得看看，被问几遍。但最近从大神那儿了解到这里面最大的坑，写出来让更多的人能受益。

<!-- more -->

先贴上很久前对着算法导论上写的算法：

```
int compare(int x, int y) {
	return (x > y ? 1 : (x == y ? 0 : -1));
}

int binary_search(int list[], int nFind, int nLeft, int nRight) {
	int nMiddle = 0;
//	while (nLeft <= nRight) {
	if (nLeft <= nRight) {
		nMiddle =  (nLeft + nRight)/2;
		switch (compare(list[nMiddle], nFind)) {
			case 0:
				{
					return nMiddle;
				}
				break;
			case -1:
				{
			//		nLeft = nMiddle + 1;
					return binary_search(list, nFind, nMiddle+1, nRight);
				}
				break;
			case 1:
				{
			//		nRight = nMiddle - 1;
					return binary_search(list, nFind, nLeft, nMiddle-1);
				}
		}
	}
	return -1;
}
```

正常的写法，递归的都有了。

一直这么写，没觉得哪有啥不对。 再仔细看看？？


`nMiddle =  (nLeft + nRight)/2;`

看出来了么？ 
还没有？

引用：http://blog.csdn.net/gangbo_5958196/article/details/9135167

这是一个经典的话题，如何计算二分查找中的中值？试卷中，大家一般给出了两种计算方法：

*	算法一： mid = (low + high) / 2
*	算法二： mid = low + (high – low)/2 

乍看起来，算法一简洁，算法二提取之后，跟算法一没有什么区别。但是实际上，区别是存在的。算法一的做法，在极端情况下，(low + high)存在着溢出的风险，进而得到错误的mid结果，导致程序错误。而算法二能够保证计算出来的mid，一定大于low，小于high，不存在溢出的问题。
回到数据库二分查找，数据库的一个索引页面(大小一般是8k或者是16k)，能够存储的索引记录是有限的，因此肯定不会出现(low + high)溢出的风险。这也是为什么InnoDB中的中值，采用的就是算法一的实现。但是，作为一个严谨的程序设计人员，还是推荐使用算法二，将任何潜在的风险，扼杀于摇篮之中。

**所以**，正确的写法应该是`mid = low + (high – low)/2`或 `mid = low + (high – low)>>1`
