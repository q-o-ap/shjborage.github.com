---
layout: post
title: "Xcode用archive只生成generic xcode archive的解决方案"
date: 2014-11-18 21:26
comments: true
categories: iOS
tags: iOS ipa 备忘
---

###引子
有这么一个工程，Archive的时候，只生成`generic xcode archive`，很奇怪为什么。想导入 ipa 或者 提交 Appstore都无从下一步。想直接使用 ApplicationLoader(在Xcode菜单中选择 Open Develop Tool 来打开) 直接打包 xxx.app 成 zip 上传，但想想又太原始。

###解决方案
无奈之下找到解决方案，备忘如下：<br>
1、点击主要工程，然后在右边分别点击PROJECT和TARGETS，在Building Settings中，搜索skip，设置为NO;<br>
2、项目中包含的其他辅助工程，点击工程文件，然后在右边分别点击PROJECT和TARGETS，在Building Settings中，搜索skip，设置为YES；对每个子工程做类似操作；<br>
3、再次archive，就正常了。<br>

###原因分析
工程文件中内嵌子工程时，才会有这个问题。
```
Skip install is NO for the main project target
Skip install is YES for framework (sub-projects) targets
```

####参考
<http://blog.csdn.net/ysysbaobei/article/details/16332677>
<http://stackoverflow.com/questions/10715211/cannot-generate-ios-app-archive-in-xcode-4-3>