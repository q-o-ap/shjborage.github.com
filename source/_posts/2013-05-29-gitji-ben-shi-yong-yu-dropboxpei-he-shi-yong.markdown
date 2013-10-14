---
layout: post
title: "Git基本使用与Dropbox配合使用"
date: 2013-05-29 11:43
comments: true
categories: Git SourceControl
---

###Git基本使用

####1.取得项目的Git仓库
#####1.1从当前目录初始化
```
$ git init
```

然后可以进行git add .. 进行文件添加。  
以及编写.gitignore文件，对本地文件进行忽略

{% codeblock OBJ-C示例 %}

 # Exclude the build directory
 build/*
 
 # Exclude temp nibs and swap files
 *~.nib
 *.swp
 18 
 # Exclude OS X folder attributes
 .DS_Store
 
 # Exclude user-specific XCode 3 and 4 files
 *.mode1
 *.mode1v3
 *.mode2v3
 *.perspective
 *.perspectivev3
 *.pbxuser
 *.xcworkspace
 *.xcuserstate
 xcuserdata

{% endcodeblock %}


#####1.2 从现有仓库克隆

```
git clone git://github.com/aaaa/aa.git
git clone file:::/Volumes/Data/xxx.git
git clone /Volumes/Data/xxx.git
git clone /Volumes/Data/xxx.git new_xxx_dir
```

默认会创建xxx目录（xxx.git）,如果想指定目录的话用最后一种

#####1.3 其它常用

```
git commit
git commit -a -m "asdf"
git status
git log
git log -1
git log -p
```

注意看提示就好了。

####2.远程仓库使用
#####2.1 创建
```
git init --bare
```

创建一个空的远程仓库（可在服务器上，也可在本地，也可以在Dropbox的目录中）


#####2.2 查看远程仓库
在本地执行以下命令
```
git remote -v
```

#####2.3 添加远程仓库
```
git remote add [shortanem] [url]
```

#####2.4 从远程抓取数据
```
git fetch [remote-name]
```

#####2.5 推送数据
```
git push [remote-name] [branchname]
```

#####2.6 其它
```
git remote show [remote-name]
git remote rename old new
git remote rm xxx
```


####Dropbox 整合

把远程建在Dropbox上即可，哈哈




