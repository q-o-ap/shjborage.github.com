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

####在你本地安装octopress

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

```
rake setup_github_pages
Enter the read/write url for your repository
(For example, 'git@github.com:your_username/your_username.github.com)
Repository url:
```

请输入：git@github.com:yourname/yourname.github.com.git (将yourname替换成你的github登录名)

这个步骤rake会做很多事情：

在.git/config中替换origin为你输入的repository，并把原来的origin写到octopress中。
创建新的branch source并切换到这个branch。
在生成的_deploy目录下，初始化git repository为你的repository。
以下是两个git config的内容，just for your information。

```
tchen@tchen-mbp:~/projects/octopress$ cat .git/config 
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
    ignorecase = true
[remote "octopress"]
    url = git://github.com/imathis/octopress.git
    fetch = +refs/heads/*:refs/remotes/octopress/*
[branch "source"]
    remote = origin
    merge = refs/heads/master
    rebase = true
[remote "origin"]
    url = git@github.com:tyrchen/tyrchen.github.com.git
    fetch = +refs/heads/*:refs/remotes/origin/*
tchen@tchen-mbp:~/projects/octopress$ cat _deploy/.git/config 
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
    ignorecase = true
[remote "origin"]
    url = git@github.com:tyrchen/tyrchen.github.com.git
    fetch = +refs/heads/*:refs/remotes/origin/*
```

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

####编写并发布文章

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

####个性化
添加个人域名
在octopress目录下：

```
$ echo 'blog.yourdomain.com' >> source/CNAME
```

然后在你的DNS服务商，如 dnspod.cn，添加相应的CNAME指向 yourname.github.com。如果你要使用顶级域名，如 http://yourdomain.com 访问你的博客，则需要使用A记录指向 207.97.227.245。详细内容请参考：http://octopress.org/docs/deploying/github/。

####设置博客

打开_config.yml，按照 http://octopress.org/docs/configuring/ 的说明进行设置即可。注意把不需要的asides都删除，免得加载不必要的js，拖累访问速度。如果想把你自己的微博个人秀加在侧栏，请参考：http://clark1231.iteye.com/blog/1553939。


####使用主题

可使用 https://github.com/amelandri/darkstripes 的主题。使用方法很简单：

```
$ cd octopress
$ git clone git://github.com/amelandri/darkstripes.git .themes/darkstripes
$ rake install['darkstripes']
$ rake generate
```

注意你对已有主题的汉化会被覆盖，请确保提交所有更改前你merge了你的改动。

####添加多说

由于github pages只支持静态文件，所以类似评论这样的功能就只能使用第三方工具。octopress自带disqus的评论系统，但其对国内用户不够友好，另外加载速度也不快。国内disqus的copycat是duoshuo，于是照猫画虎，添加多说的支持进来：

首先在 source/post/ 下创建duoshuo.html:

{% codeblock %}
{% if site.duoshuo_name %}
<!-- Duoshuo Comment BEGIN -->
    <div class="ds-thread"></div>
    <script type="text/javascript">
    var duoshuoQuery = {short_name:"{{ site.duoshuo_name }}"};
    (function() {
        var ds = document.createElement('script');
        ds.type = 'text/javascript';ds.async = true;
        ds.src = 'http://static.duoshuo.com/embed.js';
        ds.charset = 'UTF-8';
        (document.getElementsByTagName('head')[0] 
        || document.getElementsByTagName('body')[0]).appendChild(ds);
    })();
    </script>
<!-- Duoshuo Comment END -->
{% endif %}
{% endcodeblock %}

然后在 source/_layouts/post.html，将对应的disqus代码改为：
注：去掉{与%中间的\

{% codeblock %}
{% if site.duoshuo_name and page.comments == true %}
  <section id="comment">
    <h1>发表评论</h1>
    {\% include post/duoshuo.html \%}
  </section>
{% endif %}
{% endcodeblock %}

在 source/_config.yml 里，添加：

```
# Duoshuo comments
duoshuo_name: your_duoshuo_name
```

应该就可以了。可以使用如下命令测试：

```
$ rake generate
$ rake preview
```

####添加百度统计

百度统计可以将生成的script直接添加到 source/post/after_footer.html 就可以。很简单，这里就不详述。

—–EOF—–

参考：http://www.cnblogs.com/stevelin/p/3281811.html
