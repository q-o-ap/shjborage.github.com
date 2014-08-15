---
layout: post
title: "SAE python 输出log 的方法"
date: 2014-08-14 22:02
comments: true
categories: python
tags: sae, python
---

###背景
[SAE](http://sae.sina.com.cn)真是很方便，可以免费做一些简单的`Server`自己来研究用。<br>
但是学了一点`python`后，想在`SAE`里面玩一玩，发现在里面调试有点难。因为习惯了别的平台的`Debug`，一下子还是想研究，可是试了各种方案（包含`SAE`自己的`ShellMiddleware`），都没办法在`SAE`的环境中进行调试。在本地倒是研究好了`pdb`的相关使用，虽然使用起来还很初级。<br>
最后只能使用Log的方式了。

###解决方案
```
print ("xxxx %s %d" % ("asdf", 5))
# 或
logging.debug("debug log xxxxx")
```
这个Log大家应该都很熟悉啦（我还不熟悉~)，但是在`SAE`后台中如何查看呢？<br>

日志内容在 **`应用»日志中心»HTTP`** 中查看，类别为*`debug`*

可能老版本的SAE可能不支持吧，至少现在是有的。<br>
希望能对大家有所帮助。

另外，关于`logging`的使用，我也写了一个`module`，封装了一些东西。等对`python`的模块的规范熟悉一些后，会把这部分开源如来（虽然大家可能都有自己的）。