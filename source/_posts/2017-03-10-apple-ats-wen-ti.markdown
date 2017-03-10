---
layout: post
title: "Apple ATS 问题"
date: 2017-03-10 22:20
comments: true
categories: iOS
tags: iOS ATS HTTPS
---


苹果 ATS 的标准是 LTSv1.2 且 AES256 的 RSA.

有问题的两个URL：  
1. 不可以在 App 中正常获取数据
```
https://ss0.bdstatic.com/9bA1vGba2gU2pMbfm9GUKT-w/timg?nuomina&size=w9999&quality=100&sec=1418745600&di=b0c7531995ca2ccad8b63d5ad53a1fff&src=http%3A%2F%2Fe.hiphotos.baidu.com%2Fbainuo%2Fcrop%253D0%252C1%252C979%252C592%253Bw%253D230%253Bq%253D79%2Fsign%3De9a35c7a3012b31bd3239769bb281a4b%2Ff11f3a292df5e0fe030b493c556034a85edf72f8.jpg
```  
2. 可以在 App 中正常获取数据
```
https://gss0.baidu.com/7LsWdDW5_xN3otqbppnN2DJv/bainuo/pic/item/64380cd7912397dd597ec13f5e82b2b7d0a28782.jpg
```

这两个 URL 都符合标准，而且都可以使用苹果开发者论坛里面给出的方案来校验通过：<https://forums.developer.apple.com/thread/14816>

```
nscurl --ats-diagnostics https://www.example.com
```


## 解决方案

`NSURLConnection` 在iOS10上面会出现这个 9802，并不是服务器的问题。
需要切换为 `NSURLSession` 来解决这个问题。
后续的网络请求应该都不再使用 `NSURLConnection`，应该是苹果 SDK 的 bug，但因为这部分已经是不再维护的了，也别为难苹果了，使用最新的 API 吧。




