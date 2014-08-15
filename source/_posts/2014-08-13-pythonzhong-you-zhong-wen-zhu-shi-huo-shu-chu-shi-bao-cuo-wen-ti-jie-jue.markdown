---
layout: post
title: "Python中有中文注释或输出时报错问题解决"
date: 2014-08-13 23:27
comments: true
categories: python
tags: python
---

执行Python代码时，如果有中文注释，提示：
```
SyntaxError: Non-ASCII character '\xe7' in file D:\python\Test\hello\getjpg.py on line 13, but no encoding declared; see http://www.python.org/peps/pep-0263.html for details
```
与编译器还无关，不管是Sublime写完在命令行跑还是Eclipse。

**解决办法:**
在文件第一行或第二行加入以下代码：
```
# -*- coding: utf-8 -*-
```