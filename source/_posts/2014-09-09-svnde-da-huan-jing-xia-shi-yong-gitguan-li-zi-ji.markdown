---
layout: post
title: "SVN的大环境下使用Git管理"
date: 2014-09-09 22:42
comments: true
categories: SourceControl
tags: Git SVN
---

##写在前面
很多同学可能都了解`SVN`与`Git`的差异与优缺点，这部分我只是简单提一下我的观点，不是很全也不一定对，仅代表我的个人观点。如果大家对这个问题比较感兴趣的话呢，欢迎来`Google`百度一下。
###Git与SVN简单对比
####SVN
*   优点
    -   以SVN Server为中心
    -   便利的权限管理
*   缺点
    -   完全依赖中心，连接到Server才可以正常提交代码
    -   分支管理较“重”，一般就学习`trunk`, `branches`, `tags`就够了。

####Git
*   优点
    -   每个版本库都是中心
    -   非常方便的分支管理，甚至[git-flow](https://github.com/nvie/gitflow)
    -   离线管理
*   缺点
    -   权限管理（非要说的话，这个我确实没用`Git`管理过。不过pull-request可以控制一些写权限吧，也不知道这个理解对不对）

##Let's do it!
很多团队，因为权限（扯淡的控制欲）还是使用SVN，各种申请权限。少吐槽，来干货。
<!-- more -->
###`git-svn`环境
`git-svn`是`git`的一部分，安装`git`1.5.3 版本以后的都支持。Mac安装Xcode后，安装`CommandLineTool`，即可。如果是beta的版本的Mac，相信你也有能力解决好安装的问题。<br>
附上安装`CommandLineTool`的方法，在终端里执行以下命令
```
xcode-select --install
```
不确定自己行不行？在终端里执行以下命令：
```
git svn
```
如果得到以下结果，说明你OK的～<br>
<iframe src="https://www.flickr.com/photos/shjborage/15210513791/in/set-72157639886815375/player/" width="500" height="90" frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>
###`git`基本使用
分享一些使用经验，再基础的命令就不介绍了。`git`一下或者`git --help`再或者`man git`来解决吧～
####干货-`git`分支管理策略<br>
*   [git-flow](https://github.com/nvie/gitflow)
*   <http://nvie.com/posts/a-successful-git-branching-model/>(`git-flow`变种？)

####干货-实用的log，来自[这里](http://coolshell.cn/articles/7755.html)
```
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --"
```
###初始化代码库
首先用git-svn来初始化本地的代码库(repository)
```
git svn clone -s svn-repository-url
```
`svn-repository-url`部分使用svn代码库的url。如果要从trunk目录或者某个branch目录里check out，要把-s换成-T、-b等选项。具体参看`man git-svn`。这个命令时间比较长，因为需要同步所有的提交历史，还好只此一次，以后不会这么慢了。做完这一步，在本地就有了一个完整的代码库，包括所有commit的历史和log，已经可以开始用它来进行开发工作了。<br>
不过，在开始开发之前，最好先做一次垃圾搜集：
```
git gc
```
它对代码库的信息进行垃圾搜集和压缩，最明显的作用就是减小磁盘占用空间。第一次做效果尤其明显。你可以检查一下代码库的状态：
```
git status
```
现在应该在一个叫”master”的分支(branch)上。用这个命令来显示出所有的分支(branch):
```
git branch -a
```
master前有一个*号，代表你现在所处的分支，另外还有一个分支叫trunk，它是一个远程分支(remote branch)，对应的是远程svn代码库。master实际上是trunk的一个本地分支。
接下来，需要配置忽略文件，让git忽略一些目录中不希望加入代码库的文件，这部分直接参数[这个repo](https://github.com/github/gitignore)

###开发流程（建议考虑[git-flow](https://github.com/nvie/gitflow)）
这部分每个人的做法可能都有不同，全部`git`操作，上文也介绍一些，这部分如果有问题请使用Google百度一下。
####提交到`svn`
```
git svn dcommit
```
如果在git svn rebase时发生代码冲突，需要先手动解决冲突，然后用git add将修改加入索引，然后继续rebase
```
git svn rebase --continue
```
####拉取`svn`更新
```
git svn fetch
git svn rebase
```
###使用经验
使用`git-svn`当然给我们`git`的用户带来了很多方便，顺手！

*   `git`提交到`svn`还是有点水土不服：比如分支合并等都不看不出来，甚至开始的时候，我还以为本地随便提交，提到到`svn`的时候才是只有一版；
*   `git svn fetch` 下来后，不 rebase 的话，走 merge 的话，感觉流程有点奇怪（有一次直接提交 merge 到`svn`，有几次`git`上的commit都没有体现在`svn`上）。所以还是直接使用 rebase，至少 rebase 与 merge 区别，可以参考[这里](http://www.cnblogs.com/iammatthew/archive/2011/12/06/2277383.html)和[这里](http://my.oschina.net/AlexZhuang/blog/67681)；

##总结
这个`topic`我也是才开始用，可谓新手，对于`git`、`git-svn`。也在这方面摸索，如果大家有更好的方案或或者我这里提到的内容有问题，都可以跟我联系，谢谢。
##参考
<http://www.robinlu.com/blog/archives/194>
<http://www.cnblogs.com/ini_always/archive/2012/08/31/2666086.html>
<http://www.jianshu.com/p/Gy1rTq>
