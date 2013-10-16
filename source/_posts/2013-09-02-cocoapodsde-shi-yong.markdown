---
layout: post
title: "CocoaPods本身版本的更新"
date: 2013-09-02 11:00
comments: true
categories: iOS
---

CocoaPods本身也不断在更新，在项目进行中不建议进行更新。但是不得已时也得为之。
具体方法如下：

```
gem update --system
gem install cocoapods
pod setup
```

和安装其实一样的，注意版本的变化

```
pod --version
```