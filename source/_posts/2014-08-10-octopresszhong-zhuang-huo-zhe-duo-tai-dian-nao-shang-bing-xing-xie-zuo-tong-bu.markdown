---
layout: post
title: "Octopress重装或者多台电脑上并行写作同步"
date: 2014-08-10 23:41
comments: true
categories: other
tags: octopress
---

原文：<http://blog.csdn.net/hankai1024/article/details/12786201>

有日子没写博客了，换工作，忙新技术，换机器。。还是得收拾起来
时间不早了，明天还得上班，简单把这次多台机器的事记一下吧，大家如果遇到同样的问题可以看原文或者留言。

+   首先，我安装并使用过很长`octopress`，并且比较熟悉，也很好的管理着`master`与`source`分支
+   先用`Github.app`把工程`clone`下来，切换到`source`分支
+   根据`原文`的提示，另外需要把`master``clone`到`_deploy`目录中
```
cd your_blog_dir(maybe shjborage.github.com)
# 会自动创建目录，默认即master分支，如果有异常，可考虑 git clone -b master xxx
git clone git@github.com:username/username.github.com.git _deploy
```
+   再简单进行一些环境的安装
```
cd your_blog_dir(maybe shjborage.github.com)
ruby --version #Ruby 1.9.2 or later
gem install bundler # maybe sudo is needed
bundle install #install jekyll and rake ...
```
+   到目前为止，应该主要的步骤就这样了，享用吧。
+   再补充一句，如果是多台同时使用的话，管理好`source`分支即可
+   Have a good day!
