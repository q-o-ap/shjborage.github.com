---
layout: post
title: "Python - virtualenv 基础使用"
date: 2017-01-03 16:28
comments: true
categories: python
tags: python env
---


`python` 的环境折腾起来还是比较复杂，不说 macOS 下面有个默认 python，自己还需要装一个自己需要的版本（2.7或3.x）。然后不同的开发需求，可能就会使用 `pip` 安装不同的包，甚至不同的项目需要安装不同版本的包，这就麻烦了，需要 `virtualenv` 来协助了。

## 安装 `virtualenv`
`virtualenv` 不是 `python` 自带的，需要独立安装

```
pip install virtualenv
```

## 创建一个指定 `python` 版本的环境

```
virtualenv -p /usr/bin/python2.7 ENV2.7
```

## 激活

```
source ./bin/active
```

## 关闭

```
deactive
```

## Refs
[Python--Virtualenv简明教程](http://www.jianshu.com/p/08c657bd34f1)

