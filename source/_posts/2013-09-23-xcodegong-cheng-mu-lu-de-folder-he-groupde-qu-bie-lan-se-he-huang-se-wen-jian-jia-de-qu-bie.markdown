---
layout: post
title: "Xcode工程目录的 folder 和 group的区别(蓝色和黄色文件夹的区别)"
date: 2013-09-23 23:58
comments: true
categories: iOS
---

XCode工程目录里面，有时你会发现2个不同颜色的文件夹，一种是蓝色的，一种是黄色的，最常见的是黄色的，我也是最近学习html5的时候，发现还有蓝色的文件夹呢，
来上图看下吧

{% img http://e.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=fdacac7c57fbb2fb302b58177f715199/3c6d55fbb2fb4316c960de5b22a4462309f7d390.jpg?referer=1242abe23887e9501b00c65c1d8e&x=.jpg %}

 上图所示，蓝色的是folder 黄色的是group。 那么它们有什么区别呢？
1. group 和 folder的区别
group 一般只在你的工程中是文件夹的形式，但是在本地的目录中还是以散乱的形式放在一起的，除非你是从外部以group的形式引用进来的。
folder 只能作为资源，整个引用进项目，不能编译代码，也就是说，以folder形式引用进来的文件，不能被放在complie sources列表里面。
2. 以folder的形式引用文件
我拖动外部的文件夹 WebViewJavascriptBridge 到我的xcode工程目录下面
会弹出对话框，如图

{% img http://g.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=5d5b3c7fdfc451daf2f60cee86c6235b/ac6eddc451da81cb7f5527ec5066d0160924315b.jpg?referer=f1cbd2b88026cffc303d8a827f3a&x=.jpg %}


copy，是指文件不在项目的目录下面，例如项目在桌面上，文件在 Download 下，你 copy 就会被复制到项目里面。如果文件原本就在项目里面，则不执行任何操作。这里不管它，重点是 下面的单选框。
这里我选择的是以folder的形式引用文件夹，点击完成，如下图所示，文件夹是蓝色的，并且编译文件列表里面没有 webviewjavascriptBridge.m这个文件，证明文件夹引用，只会引用文件夹，文件夹里面的东西都会直接拷贝到 bundle 包，不参与编译。

{% img http://d.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=30921610a38b87d65442ab1a37335905/8601a18b87d6277f1da196192a381f30e924fc64.jpg?referer=a32ad7320bf79052b608720e3c3a&x=.jpg %}

2. 以group的形式引用文件
回到1的第一步，选中 create groups for any added folders ，点击完成，则如下图所示，文件夹是黄色的，并且编译文件列表里面神奇的存在了 webviewjavascriptBridge.m这个文件


{% img http://b.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=c6f8e8200855b31998f982707392f31b/78310a55b319ebc4de7ed2b88026cffc1e171688.jpg?referer=74ae9b6a00087bf424fb62d95287&x=.jpg %}


现在，终于明白了 folder和 group的区别了。