---
layout: post
title: "Markdown中表格与它常见效果实现"
date: 2014-07-09 23:26
comments: true
categories: other
tags: Markdown
---

[Markdown](http://zh.wikipedia.org/wiki/Markdown) 的使用越来越广，大多数时候我们也能简单的使用，各种教程也比较多。  
这里 <http://wowubuntu.com/markdown/> 也有简单中文的语法说明。

但是有些效果还是无从查证，比如表格，`<del></del>`这种。
这两者可以使用 `html` 直接来实现，哈哈，尤其是后者。
对于表格来说，还有一种简便方法，但仅限于部分环境（PHP Markdown Extra, Mou，Github）。

```
你好|Markdown|表格
---|---|---
我|不是|人
```

 可惜的是Octopress不支持，不过博客里用的也少，简单记录一下吧。