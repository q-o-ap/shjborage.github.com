---
layout: post
title: "Emacs 30分钟基础教程"
date: 2013-10-26 23:36
comments: true
categories: python other
tags: Tools
---

###简介

没有听说过 Emacs 吗？没有关系，花上30分钟就能了解和熟悉这个古老而强大 的编辑器。他被很多人称作世界上最强大的编辑器。用户从无数高手到IT民工到 普通文本编辑器用户，可以符合几乎所有人的需求。甚至可以在 Emacs 里运行 音乐播放器、网页浏览器等等，Emacs 更像是一个操作平台。

使用 Emacs 可以完全抛弃鼠标，不必在打字的同时把手移开键盘找鼠标，不必在 一大堆深藏不露的菜单里寻找那些常用功能，也不必被一条条工具栏遮挡视线。 大多数的 Windows 编辑器都有一条菜单栏，n条工具栏。菜单里有无数选项，很 多选项都会弹出一个对话框，这个对话框又会带有层层叠叠的标签，每个标签里 又有很多输入框很多按钮。一个简单的把所有字母大写的命令就要在菜单里翻半 天，说不定还找不到，而他们的帮助信息又总是不知所云。而 Emacs 的大多数操 作都可以通过快捷键或直接输入命令完成，而且有着极好的帮助系统，可以通过 多种方式找到你想要的命令或按键。

不要被 Emacs 简单的外观和与众不同的操作方式迷惑，上手 Emacs 并不是什么 难事，看一遍 Tutorial 就可以了，Emacs23 已经带着多种语言版本的快速指南， 包括中文。仍然觉得 Tutor 太长？那可以继续看本文。

<!-- more -->

###按键说明

首先说明一下，Emacs 的按键表示方法。最常用的是 Ctrl 和 Alt 的组合键， 分别用 C 和 M 表示，比如:

C-x 就是 Ctrl+x，
M-x 就是 Alt+x，
C-M-f 就是 Ctrl+Alt+f，
M-% 就是 Alt+%，实际上是 Alt+Shift+5
C-x C-f 就是 Ctrl+x 然后再按 Ctrl+f，中间可以一直按着 Ctrl 也可以松 开，无所谓间隔多长时间。
这样你就可以简单的用 C 和 M 表示 ctrl 和 alt 了。Shift 可以用 S 表示。

键盘上的特殊按键用<>表示，比如回车，空 格，方向键上， 等等，一看就知道什么意思。

不必追究为什么把 alt 写为 M，因为你这次只有30分钟时间。

###下载及安装

####Windows 用户

Windows 用户可以去 http://ntemacs.sf.net 下载可执行文件或者源代码。建议 下载Emacs23.x 。这是一个 7zip 的自动解压缩包，解压到你喜欢的目录就可以 了。

到这里你大概用了5分钟，当然，下载和解压缩时间不算在内。:)

####Linux 用户 (windows 用户可以忽略)

可以下载 CVS 的 Emacs 代码，编译一下就好了。下载 CVS Emacs23:

```
$ cvs -z3 -d:pserver:anonymous@cvs.savannah.gnu.org:/sources/emacs co -r emacs-unicode-2 emacs
```
编译:

```
$ cd emacs $ ./configure —enable-font-backend —with-xft —with-freetype —with-gtk
```
注意 configure 的输出，是否缺少什么东西，没有问题的话就可以 make:

```
$ make bootstrap
```
运行一下 src/emacs 如果没有问题的话就可以安装到默认路径：

```
$ sudo make install
```

###常用命令

####启动和退出

Windows 用户可以用 Emacs bin 目录下的 runemacs.exe 启动，Linux 用户键 入 emacs & 就可以了。

启动 Emacs 后应该能看到界面  
退出 Emacs 按 C-x C-c (就是 Ctrl+x 接着按 Ctrl+c)。

###打开和保存文件

启动 Emacs 后，按 C-x C-f，minibuffer (就是 Emacs 最下面一条) 会提示路 径，你可以在这里输入路径名和文件名，或者回车看到这个目录列表。可以 用方向键移动光标到文件名上回车打开文件，或者在子目录上进入子 目录，在 .. 上进入上一层目录。

打开文件后像往常一样编辑，随便按字母键输入一些字符。按 C-x C-s 保存文件。按 C-x k 回车关闭这个 buffer。

**小结：按键说明**

这里可以看到很多 C-x 开头的组合键，这些通常都是 Emacs 预先定义，全局性 的命令。按了 C-x 不动，一秒钟后 minibuffer 提示 C-x- ，表示 Emacs 正在 等待用户按下一个键。如果你想取消这次按键，可以接下去按 C-g，minibuffer 提示 C-x C-g is undefined 也就是说 Emacs 什么都没有执行。C-g 一般都是取 消命令，取消按键。如果按了 C-x C-h 就会出现一个帮助 buffer，里面是以 C-x 开头的按键帮助。C-h 是一个帮助按键。如何获得更多的帮助，会在之后的 小节介绍。C-c 开头的按键通常是用户自定义的按键和扩展包或扩展模式定义的 按键，同样也可以获得帮助信息。

###选定区域，剪切、复制、粘贴

好了，到现在为止你大概一共花了 10 分钟吧。

你可以用鼠标拖选一块区域。当然，最好用键盘。按 C-@，minibuffer 提示 Mark set，设定了一个 mark。然后移动光标，这时候你就选定了光标和 Mark 之 间的区域。什么都看不到？你可以按 C-w，这块区域就消失了，C-w 是剪切。按 C-x u 可以撤销上一次的剪切，C-x u 是取消。你肯定想看到你到底选中了哪块 区域，可以按M-x 然后输入 transient-mark-mode ，回车。这次你就可以看到你 选中了哪块区域了。M-x 是执行 Emacs 命令。

【插播广告】

如果是德语键盘，在 Windows 下很可能按不出 C-@, 而 C-又被输入法占去了。所以最好自己设定 set-mark 的按键。比如把 C-" 设为 mark 命令。按 C-: (ctrl-冒号) 输入下面这句话，然后回车：

(global-set-key (kbd "C-\"") 'set-mark-command)
可以在你的.emacs文件里加入类似的语句

【广告结束】

Emacs 有一个很有用的功能，在你选定了区域之后按 C-x C-x 可以交换光标 (point) 和 Mark 的位置，这样你就可以修改调整你选定的区域了。

按 C-g 取消选定的区域。

重新用 C-@ 选定一块区域，按 M-w，这是复制，移动光标到别处，按 C-y，这是 粘贴。紧接着再按一次 M-y，这时可以看到粘贴的内容变成了上一次剪切的内容。 再按 M-y 又变成之前一次复制的内容。

C-k 可以剪切一行。连续的 C-k 会把剪切的内容放在一起，一次 C-y 就可以把 之前的 n 行一起贴上。

C-x u 可以撤销，不过这个撤销和常规 windows 程序的撤销不太一样。按了 C-x u 撤销后，如果移动一下光标，再按一次 C-x u，可以撤销之前的撤销命令。 这样相当于是 redu。

小结：

C-@ 选定区域
C-w 剪切
M-w 复制
C-y 粘贴
M-y 粘贴剪贴板之前的内容
C-k 剪切一行
C-x u 撤销

###查找

其 他 windows 程序的查找通常会跳出一个对话框，这很讨厌，会遮住文本内容， 有时候我就会忘了我到底要找什么。Emacs 就不会这样。按 C-s 是查找， minibuffer 提示 I-search: 接下来你可以输入你要找的词，比如想找 windows， 输入 w，Emacs 开始找到 w，光标移动到 w 上，接着输入 i，Emacs 找到 wi， 并注意到Emacs 会同时高亮所有的 wi，接着输入 n，Emacs 找到 win，等等。这 就是 incremetal search，它的好处是，你想要找 windows，但实际上你只要输 入 win 就能定位到 windows 上了，不需要继续输入 dows 了。继续按 C-s 找下 一个匹配的。C-r 往回查找。按回车或者方向键结束本次查找，再按两次 C-s 重 复上次查找，按 C-g 取消查找，光标回到原处。

C-s 或 C-r 也可以用作光标定位，比如想要移到前面 Emacs 处，就可以 C-r Emacs，效率很高，可以说是指哪打哪。

如果查找输入的全是小写，那么这次查找是大小写不敏感的，如果输入字符中有 大写字母，那么这次查找就是大小写敏感的。很智能吧。

C-M-s 或 C-M-r 是正则表达式查找。或者在 C-s 之后按 M-r 也可以切换到正则 表达式查找。这里不仔细讲 Emacs 的正则表达式是怎么样的。那样会多花一个小 时。

替换

M-% 是 query-replace，输入查找内容和替换内容，然后 Emacs 会询问是否要 替换，y 替换，n 不替换，! 替换光标之后所有，q 结束，? 可以得到帮助。

Emacs 的替换有一个很好的特性，可以只在选定区域内进行替换。这样就可以放 心大胆地使用 ! 替换所用，而不用担心超出范围了。

C-M-% 是正则表达式的替换。

小结：

C-s 和 C-r 向前向后查找
C-% 替换
C-M-s 和 C-M-r 向前向后正则表达式查找
C-M-% 正则表达式替换
###寻求帮助

到这里你用了大概20分钟，基本的文本编辑功能都有了。

接着你可以自己摸索了。

Emacs 提供了极其强大的帮助系统。

C-h t 是一个 tutorial，现在也有不同语言的 tutorial。按 M-x 输入 help-with-tutorial-spec-language 可以选择不同的语言。按 Emacs 可 以帮你补全命令。按两次 会有补全的提示。

C-h a 是 apropos command，你可以输入几个简单的单词，Emacs 可以帮你找到 相应的命令，这个帮助非常好用。比如你想把一个单词全部大写，但不知道该怎 么做，可以 C-h a 输入 upcase word 或者 up word，Emacs 就会找到几个命令， 其中一个是 upcase-word，并且对应的按键是 M-u，你只要把光标移动到单词前 按 M-u 就可以了。

C-h k 是描述某个按键会执行什么。例如你要看看 M-; 是干什么的，按 C-h k， 接着按 M-; 你就会看到 M-; 会运行 comment-dwim，dwim 是 do what i mean 的缩写，这个命令会注释或反注释你选定的区域，如果没有选定区域，会在这一 行后面加上注释。很聪明的命令吧。

C-h c 简单描述某个按键执行什么。

C-h f 描述某个函数

C-h v 描述某个变量

C-h 会提供很多很多帮助信息，C-h ? 会显示有哪些帮助信息。

###简单配置

Emacs 有超强的可配置性，1000个人可以有1000种配置，可以有自己的键绑定， 有自定义的函数，有自己喜欢的扩展包。配置放在 home 目录下的 .emacs 里， Emacs 启动的时候会自动读取这个文件。启动 Emacs 后，C-x C-f ~/.emacs 会 打开 .emacs 文件，如果不存在的话就是新建这个文件。home 目录在哪里呢？ 可以 M-x getenv home 得 到 home 的位置。Linux 系统很好理解。 Windows 系统，会默认放在 C:\Documents and Settings\username\Application Data 下，有几种方法可以指定 home 的位置：

在 我的电脑->属性->高级->环境变量 里添加一个 HOME 环境变量，指定位 置。
在注册表 [HKEY_CURRENT_USER\SOFTWARE\GNU\Emacs] (如果没有 GNU 或 Emacs，添加一个即可) 添加 HOME 字符串变量，值为指定的 home 路径。
Emacs 配置使用的语言是 Elisp，Emacs 附带非常详细的 Elisp 手册，可以通 过 M-x info 得到。配置 .emacs 一般只需要几个简单语句，比如：

(setq variable-name value) 设置变量值，例如 (setq transient-mark-mode t) 设置选定区域范围高亮
(require 'extension) 安装一个扩展包。
(load "file-name") 使用一个 .el 文件
(global-set-key (kbd "C-c f") 'function-name) 全局自定义键绑定
(define-key dired-mode (kbd "j") 'dired-next-line) 局部键绑定，只在 dired-mode 起作用
(if ...) 一些简单的判断语句
这里提供一个简单的 .emacs 文件: quick.emacs.el

你可以把它复制到你的 home 目录下并改名为 .emacs 注意有个.

或者用 runemacs.exe -q -l quick.emacs.el 来指定 emacs 使用这个配置文件。

###总结

到这里差不多就是30分钟吧，介绍一些文本编辑的基本操作。只是为了说明上手 Emacs 其实很简单，而且几乎每一样操作都有比普通 Windows 文本编辑器更好 的特性，比如可以任意调整已选定的区域，更好的复制粘贴机制，更好的撤销机 制，更好的 isearch 查找方式，可以在区域内替换，强大得多的帮助系统，没 有任何限制的扩展性。用了几天或几个星期后就会觉得其他的编辑是多么麻烦和 弱小。
