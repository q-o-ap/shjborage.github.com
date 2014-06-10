---
layout: post
title: "MAC 系统的启动过程和系统启动时运行shell 脚本"
date: 2013-10-19 01:34
comments: true
categories: Mac
tags: Mac
---

####1 Mac 开机启动脚本或应用（系统启动时运行shell 脚本）
1）如果想启动的是一个Mac 的应用程序（即xxx.app）则可以通过图形化界面来设置：

    system prefernce-->Accounts-->LogItems-->add
 
2）把想要启动的shell 或应用放到/Labrary/Startupitems/ 目录中。

3）*创建/etc/rc.local 文件，把脚本写在文件中*。

<!-- more -->

4）如果只启动shell 程序，还有一种方式，大体流程是这样的。
    A) 把Terminate 设置为开机启动。这样当系统启动时，可以自动运行shell。
    B) 在用户目录下创建或修改".profile"。当shell 启动时，先运行".profile" 文件
    C) 在".profile" 中调用想要执行的shell 脚本
    
5)  应用Mac 的launchd，可以参见：http://cb.esast.com/cb/wiki/10020
 
苹果的安全检查：用上面说过的2，3 方法时，必须设置文件的用户id 和组id，还有文件的运行权限。具体方法，下文有述。
 
当设置完成后，可以在终端上用SystemStarter 来调试配置的信息
命令为：`SystemStarter -n -D`
根据提示的信息可以查看配置的问题。
 
 
####2 解决“Insecure Startup Item disabled.错误”
可以把想要启动的shell 放到/Labrary/Startupitems/ 目录中。
必须改变文件的用户id 和组id 才可以，并且权限为755

```
sudo chown -R root:wheel /Library/StartupItems/xxx
sudo chmod -R 755 /Library/StartupItems/xxx
```

(如果运行的是shell 文件，则命令中不用加 -R 选项)
 
 
注意：环境变量问题。

当然一定要参考苹果的开发者文档：
[Introduction to System Startup Programming Topics]
(http://developer.apple.com/mac/library/documentation/MacOSX/Conceptual/BPSystemStartup/BPSystemStartup.html#//apple_ref/doc/uid/10000172-SW1)