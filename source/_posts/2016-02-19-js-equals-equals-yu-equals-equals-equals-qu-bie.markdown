---
layout: post
title: "JavaScript ==与===区别（两个等号与三个等号）"
date: 2016-02-19 10:59
comments: true
categories: JavaScript
tags: JavaScript JS
---

### 对于 string, number 等基础类型，== 和 === 是有区别的
*   不同类型间比较，== 之比较“转化成同一类型后的值”看“值”是否相等，===如果类型不同，其结果就是不等
*   同类型比较，直接进行“值”比较，两者结果一样

### 对于 Array, Object 等高级类型，== 和 === 是没有区别的
*   进行“指针地址”比较

### 基础类型与高级类型，== 和 === 是有区别的  
*   对于==，将高级转化为基础类型，进行“值”比较
*   因为类型不同，===结果为false  


> 20160912 update    

### 对于 null 与 undefined, == 和 === 是有区别的
*   如使用 `==` 的话，`undefined` 会被转换成 `null`，所以很多判断是否为 `null` 可以使用 `==`.

