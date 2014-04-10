---
layout: post
title: "Reveal插件简介"
date: 2014-04-08 16:24
comments: true
categories: iOS
---

Reveal 自己没有这种插件，可以修改工程使用，就自己做个吧。  
做的时候参考了Spark Inspector。

<https://github.com/shjborage/Reveal-Plugin-for-XCode>

<!-- more -->

Plugin for Xcode to integrate the Reveal App to your project automatic(Without any modifications to your project).

## Introduction

**The Reveal Plugin works just like Instruments.**


The Spark Inspector integrates directly into Xcode, so you don't need to add frameworks or modify your project to inspect your app in the iOS Simulator.

If you're in the middle of a debug session and something unexpected happens, you can attach the Spark Inspector to see what's going on. No restarting your app, no changing your project. All from inside Xcode.


## Install

###Via Alcatraz

Reveal-Plugin-for-XCode is available through the [Alcatraz](http://alcatraz.io/) Xcode plugin management system.

From source

Simply build the Xcode project and restart Xcode. The plugin will automatically install to `~/Library/Application Support/Developer/Shared/Xcode/Plug-ins`.

## Uninstall

To uninstall, just remove the plugin from the above directory, and restart Xcode:

```
rm -rf ~/Library/Application\ Support/Developer/Shared/Xcode/Plug-ins/RevealPlugin.xcplugin
```

Tested on Xcode 5.1 and Mac OS X 10.9.2.

## Contributors

Welcome to fork and PullRequest to do this better.
We use issues to manage bugs and enhanced features.

## Thanks
	
+	[Integrating Reveal without modifying your Xcode project](http://blog.ittybittyapps.com/blog/2013/11/07/integrating-reveal-without-modifying-your-xcode-project/)
+	[Xcode 4 插件制作入门](http://onevcat.com/2013/02/xcode-plugin)
