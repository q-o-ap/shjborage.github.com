---
layout: post
title: "Git no branch 解决方案"
date: 2013-06-07 18:03
comments: true
categories: SourceControl
tags: SourceControl Git
---

Git在使用过程中，不断的Checkout，以及各种工具使用执行各种平时不太接触的命令，最后发现push不了。 仔细检查才发现原来木有在任何branch上面开发

#####事件经过：
1. 正常开发，branch:master
2. 在Eclipse中，使用插件checkout某版本出来
3. 再checkout到最近一次提交的版本
4. 不断Commit多次，一直不能push，以为工具出了问题
5. 实在忍受不了就找问题，最后git branch发现 *(no branch).

每次都提交，数据肯定不会丢，也没有担心。
就找个问题的解决方案，果然有。

<!-- more -->


http://serverfault.com/questions/56722/git-seemed-to-be-in-no-branch-and-then-lost-my-changes
{% codeblock %}
# if you have already checked out to master, 
# you won't know the commit-ish of your "no branch":

git fsck --lost-found # (to find your <commit-ish>)
git merge <commit-ish>

# if you are still on your "no branch" commit:

git log # (the commit-ish will be on the first line)
git checkout master
git merge <commit-ish>

# or

git log | head -n 1 | cut -d ' ' -f 2 | pbcopy
git checkout master
git merge <commit-ish>

{% endcodeblock %}