---
layout: post
title: "给xcode工程做命令行build"
date: 2013-10-14 14:59
comments: true
categories: iOS
---

XCode工程其实是可以用命令行来build的，这样就可以用命令行编译出.app的文件，然后就可以打成ipa包。整个流程做到自动化后，就可以方便的做每日的daily build，便于产品和测试同事跟进进度，随时获得最新版本的ipa文件。

XCode工程的命令行Build命令是 xcodebuild，用 --help 可以看到一些有用的参数。常用的是xcodebuild -configuration Release 指定用Release方式编译。
  
另外，将.app文件转成ipa包的方式也相当简单。具体方式是将 .app文件拷贝到一个名为 ipa/Payload的目录下，然后将ipa下的所有内容用zip打包即可：

```
      mkdir -p ipa/Payload
      cp -r ./Release-iphoneos/PRODUCT_NAME.app  ./ipa/Payload/
      cd ipa
      zip -r $FILE_NAME *
```


如果要将打好的ipa包上传到服务器上面去，再加一些脚本即可，我们是放到一个再windows共享的web server目录下，于是可以用smbclient来连接windows共享和上传ipa包：

```
      echo "put FILE_NAME.ipa" | smbclient //主机名 -U 用户名%密码
```


附上所有想着的XCode命令


######1.查看xcode的版本号和build版本

```
$ xcodebuild -version
```

显示内容：

```
Xcode 4.2Build version 4D199
```
 
######2.显示当前系统的sdk、及其版本

```
$ xcodebuild -showsdks
```

显示内容：

```
Mac OS X SDKs:    Mac OS X 10.6                     -sdk macosx10.6    Mac OS X 10.7                     -sdk macosx10.7iOS SDKs:    iOS 5.0                           -sdk iphoneos5.0iOS Simulator SDKs:    Simulator - iOS 5.0               -sdk iphonesimulator5.0
```

 
######3.显示工程项目信息
先cd到工程目录下（有＊.xcodeproj的目录，比如MakeFileTest.xcodeproj），然后输入命令 
```
$ xcodebuild -list
```

显示内容

```
Information about project "MakeFileTest":    
Targets:        MakeFileTest    
Build Configurations:        Debug        Release    If no build configuration is specified and -scheme is not passed then "Release" is used.    
Schemes:        MakeFileTest
```

如果未指定Configurations，默认为release
 
######4.xcodebuild用法：
#######1）用ios5.0模拟器（iphonesimulator5.0）建立工程

```
$ xcodebuild -sdk iphonesimulator5.0
```

输出一堆编译过程，最后输出** BUILD SUCCEEDED **表示创建成功；
若输出** BUILD FAILED **表示创建失败
成功后会在当前工程目录下创建build目录，结构如下：

<a href="http://www.flickr.com/photos/105999540@N03/10375710016/" title="2013-10-14-1 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7455/10375710016_54e25948d4.jpg" width="500" height="60" alt="2013-10-14-1"></a>

MakeFileTest.app就是编译生成的可执行文件，
而MakeFileTest.build文件夹下是一些中间文件

<a href="http://www.flickr.com/photos/105999540@N03/10375710126/" title="2013-10-14-2 by EricShj, on Flickr"><img src="http://farm3.staticflickr.com/2894/10375710126_bfaca5c32b.jpg" width="500" height="98" alt="2013-10-14-2"></a>

#######2）默认是Release工程，要设置成Debug模式如下：
```
$ xcodebuild -sdk iphonesimulator5.0 -configuration Debug
```
#######3）clean创建的工程
```
$ xcodebuild clean -sdk iphonesimulator5.0 -configuration Debug
```
成功输出**CLEAN SUCCEEDED**
 
######5.xcodebuild的编译参数


下面说运行从命令行中运行应用程序:


需要一个小工具iphonesim,从 https://github.com/hborders/iphonesim/ 下载后编译成为一个可执行文件,用它来启动iphone模拟器.
由于模拟器不支持多个实例,所以运行前记得先把模拟器给关掉。按照以下命令来执行吧!

```
iphonesim launch /Users/TadoSoft/Library/Application\ Support/iPhone\ Simulator/4.3.2/Applications/ED845558-5168-45C0-BA98-43AB3C238E9F/Krowdit.app/
```