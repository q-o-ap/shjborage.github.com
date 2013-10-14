---
layout: post
title: "在github上使用octopress"
date: 2013-08-22 10:55
comments: true
categories: other
---

Octopress是使用ruby语言编写的一套建立在jekyll之上的博客系统，它有一些很独特的特点：

1. 使用静态页面；
2. 不使用数据库；
3. 使用markdown标记语言编写文章；
4. 可以与git紧密集成，方便地进行博客的版本管理；
5. 可以于Github Pages集成，不需要单独的web hosting，只要你有github帐号即可。


这些特点中的第三点到第五点尤其吸引我（第一点和第二点也很酷），于是我花了些时间研究。以下是相关的步骤和心得。

在你本地安装octopress

说“安装”可能不太恰当，准确的说是你需要从github上取得octopress的代码并进行些许配置。步骤请参考这里。

1. 安装RVM(Ruby Version Manager)并使用”Ruby 1.9.2″作为你本地的ruby实现，这事我之前按照这篇文章做过，这里就不详细说了，你也可以按照octopress官方的介绍来安装RVM。

2. 从github得到octopress代码，运行以下命令：
{% codeblock %}
git clone git://github.com/imathis/octopress.git octopress
cd octopress    # If you use RVM, You'll be asked if you trust the .rvmrc file (say yes).
ruby --version  # Should report Ruby 1.9.2

gem install bundler # Install dependencies
bundle install

rake install # Install the default Octopress theme
{% endcodeblock %}

配置你本地的octopress，使之可以发布到你的github pages

这样就可以做到：你在本地创建博客文章(当然是用markdown写成的)，然后直接deploy到你的github pages repo，github会自动进行发布。也就是说：所有发博客的动作只需要在你本地用命令完成，酷吧！

参考了这里<http://octopress.org/docs/blogging/>。

1. 创建你的github pages repo

如果你的github用户名是username，那个就创建一个名称为”username.github.com”的repo，这个repo就是你的github pages repo，更多信息可以参考这里。

2. 在你本地运行以下命令，octopress会自动帮你进行配置：
```
cd your_local_octopress_directory
rake setup_github_pages
```
注意：当命令提示你输入github URL时，请使用这个格式的URL: “git@github.com:username/username.github.com.git”

当这个命令运行完成之后，你的github pages repo会有两个branches: master branch用于静态页面的发布(“octopress/_deploy”的内容)，source branch用于保存你的blog内容（”octopress”目录中除了”_deploy”的其它内容）

到这里基本的配置就完成了，你还可以参考这里配置你博客的名称等信息。

编写并发布文章

1. 写文章

cd your_local_octopress_directory
rake new_post["Your Title of Your Article"]
该命令会在你的”octopress/source/_posts”目录下生成对应的”.markdown”文件，用任意文本编辑器编辑，使用markdown语法编写你的文章。

2. 生成，预览并发布：
```
cd your_local_octopress_directory
rake generate # generate your blog static pages content according to your input. 
rake preview # start a web server on "http://localhost:4000", you can preview your blog content.
rake deploy # push your static pages content to your github pages repo ("master" branch)
```
还有很重要的一步是把你的修改(文本修改，不包含”_deploy”目录，”deploy”目录保存”rake generate”生成的静态页面内容，会被”rake deploy”命令提交到”master branch”)放到你的github pages(“source” branch)上:
```
cd your_local_octopress_directory
git add .
git commit -m 'your message'
git push origin source
```
—–EOF—–
