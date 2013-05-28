---
layout: post
title: "iOS 开发常见低级错误"
date: 2013-05-28 10:19
comments: true
categories: iOS
---

***不断更新中。。。***


May 28, 2013

*	###不要调用[super  release] 
	在dealloc里要调用[super dealloc]，千万不要调用[super release]
