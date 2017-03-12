---
layout: post
title: "Linux 开发备忘"
date: 2017-03-12 11:00
comments: true
categories: Mac Web Other
tags: Linux BackEnd 
---


## Terminal 使用相关（Mac 下测试通过）

### [.bash_profile生效方法](http://blog.csdn.net/kai27ks/article/details/7449610)

```
source .bash_profile
exec bash --login
```

### 获取当前时间的unix时间戳
from: <http://www.jb51.net/LINUXjishu/227114.html>

```
date +%s
```

### awk 命令
<http://www.cnblogs.com/ggjucheng/archive/2013/01/13/2858470.html>

### grep 查询日志
[linux grep命令](http://www.cnblogs.com/end/archive/2012/02/21/2360965.html)

```
grep -n "xxx" log/xxx.2016xxxxx* 
```

### 压缩解压

```
// unzip
tar -zxvf xxx.gz

// zip a dir
tar -zcvf xxx.gz dirName
```

### Terminal 光标移动

<http://blog.csdn.net/teng_ontheway/article/details/50159891>

```
Ctrl + a    // 回行首
Ctrl + e    // 回行尾
```

<!-- more -->

### 端口占用查询

命令 `lsof -i tcp:port`（port替换成端口号，比如 6379）可以查看该端口被什么程序占用，并显示PID，方便KILL

## Vim 相关

控制鼠标模式，目前还是比较适应纯键盘输入。

```
set mouse=v
```

### Vim插件体系管理系统
https://github.com/VundleVim/Vundle.vim
https://github.com/szw/vim-tags
快捷键 `Ctrl+]`

这个插件体系也可以完全不用的~~

#### ctags
其实是个索引文件，需要创建，并且在当前目录打开才可以使用的。

```
// 创建tags文件
cd /your work path
ctags -R *

// vim 中跳转与返回
Ctrl + ] 
Ctrl + T
```

##### ctags 更好的支持 php

在 `~/.ctags` 中添加这段，更好的创建 php 文件的 tags 文件。

```
--regex-php=/^[ \t]*[(private|protected|public|static)( \t)]*function[ \t]+([A-Za-z0-9_]+)[ \t]*\(/\1/f, function, functions/
--regex-php=/^[ \t]*[(private|protected|public|static)]+[ \t]+\$([A-Za-z0-9_]+)[ \t]*/\1/p, property, properties/
--regex-php=/^[ \t]*(const)[ \t]+([A-Za-z0-9_]+)[ \t]*/\2/d, const, constants/
```

#### taglist
#### WinManager
#### phpcomplete
#### supertab

```
"phpcomplete
filetype plugin on
autocmd FileType php set omnifunc=phpcomplete#CompletePHP

" taglist
let Tlist_Show_One_File=1
let Tlist_Exit_OnlyWindow=1

" wm
let g:winManagerWindowLayout='FileExplorer|TagList'
"nmap wm :WMToggle<cr>

" 设置winmanager
" 设置界面分割
"let g:winManagerWindowLayout = "TagList|FileExplorer"
"设置winmanager的宽度，默认为25
let g:winManagerWidth = 30
"定义打开关闭winmanager快捷键为F8 => 改为普通模式的 wm
nmap wm :WMToggle<cr>
"在进入vim时自动打开winmanager
let g:AutoOpenWinManager = 1

" supertab
let g:SuperTabRetainCompletionType=2
let g:SuperTabDefaultCompletionType="<C-X><C-O>"
```

![](http://shjborage-public.qiniudn.com/2017-03-12-14776418022718.jpg)

##### 代码自动补全升级
[VIM代码补全提示功能](http://blog.csdn.net/arcsinsin/article/details/39436043)

插件是这个：[autocomplpop](http://www.vim.org/scripts/script.PHP?script_id=1879)
安装后，在 `.vimrc` 中添加配置，支持 php 的 `::`, `->`, `$`：

```
if !exists('g:AutoComplPop_Behavior')
let g:AutoComplPop_Behavior = {}
let g:AutoComplPop_Behavior['php'] = []
call add(g:AutoComplPop_Behavior['php'], {
\ 'command' : "",
\ 'pattern' : printf('−>|::|$\k\{『百分号』d,}$', 0),
\ 'repeat' : 0,
\})
endif
```

#### NERDTree

```
 114 "NERDTree
 115 let g:NERDTree_title="[NERDTree]"
 116 let g:winManagerWindowLayout="NERDTree|TagList"
 117
 118 function! NERDTree_Start()
 119     exec 'NERDTree'
 120 endfunction
 121
 122 function! NERDTree_IsValid()
 123     return 1
 124 endfunction
```

<http://blog.csdn.net/bokee/article/details/6633193>

这个版本的Winmanager好像有个小bug，你在打开Winmanager界面时，会同时打开一个空的文件。这会影响后续使用，所以我们要在打开Winmanager时关掉这个空文件。在~/.vim/plugin目录下的winmanager.vim文件中找到以下函数定义并在第5行下添加第6行的内容：

```
function! <SID>ToggleWindowsManager()  
   if IsWinManagerVisible()  
      call s:CloseWindowsManager()  
   else  
      call s:StartWindowsManager()  
      exe 'q'
   end  
endfunction
```

### Vim 打开多窗口、多文件之间的切换

打开多个文件：
一、Vim 还没有启动的时候：``
1.在终端里输入 
```
vim file1 file2 ... filen便可以打开所有想要打开的文件
```
2.Vim已经启动
输入
```
:e file
```

可以再打开一个文件，并且此时vim里会显示出file文件的内容。

3.同时显示多个文件：

```
:sp         //水平切分窗口
:vsplit     //垂直切分窗口(:vsp)
```

二、在文件之间切换：
1.文件间切换
```
Ctrl+6  //两文件间的切换
:bn      //下一个文件
:bp      //上一个文件
:ls       //列出打开的文件，带编号
:b1~n  //切换至第n个文件
```

对于用 `(v)split` 在多个窗格中打开的文件，这种方法只会在当前窗格中切换不同的文件。

2.在窗格间切换的方法

```
Ctrl+w+方向键——切换到前／下／上／后一个窗格
Ctrl+w+h/j/k/l ——同上
Ctrl+ww——依次向后切换到下一个窗格中
```




