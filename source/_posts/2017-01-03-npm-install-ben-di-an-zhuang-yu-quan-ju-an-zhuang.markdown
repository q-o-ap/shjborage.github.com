---
layout: post
title: "npm install 本地安装与全局安装"
date: 2017-01-03 17:30
comments: true
categories: JavaScript
tags: npm js JavaScript
---

研究 `node.js` 时，发现使用 `javascript` 也可以写 `shell` 脚本，用到了下面这个命令：

```
npm install shelljs --save-dev
```

顺带研究了一下 `npm install` 的使用，如果不带 `-g` 的话，是保存在当前目录下的【本地安装】，否则是全局安装。

`--save` 与 `--save-dev` 这个适用于有 `package.json` 的情况。
保存在 `dependencies` 或 `devDependencies`中。

## Ref
[npm install —— 从一个简单例子，看本地安装与全局安装的区别](https://yq.aliyun.com/articles/36217)  
[请问 npm install --save 与 npm install --save-dev 的区别](http://cnodejs.org/topic/54d33df497be414b68dcf503)



