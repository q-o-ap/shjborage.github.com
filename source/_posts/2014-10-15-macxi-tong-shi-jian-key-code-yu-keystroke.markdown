---
layout: post
title: "【翻译】Mac系统事件，Key Code 与 KeyStroke"
date: 2014-10-15 10:11
comments: true
categories: Translate
tags: Mac Script Translate
---
###引子 
业余时间写了一个 [Reveal](http://revealapp.com/) 的[插件](https://github.com/shjborage/Reveal-Plugin-for-Xcode)，其中涉及一些 MacOS 的开发以及 AppleScript 相关的东西，看到这个文章，比较受用，翻译出来分享给大家。
做这个事一方面是为了方便大家使用相关的技术，同时也能锻炼自己的翻译、文笔等，大家共同进步嘛。

###正文

## Mac 系统事件，Key Code 与 KeyStroke

**Key codes** 指代码键盘上对应键位的数字编码。**KeyStroke** 是实际的可见的键值。你可以使用这些数字编码或键值在 AppleScipt 中通过 "System Events" 来模拟键盘上某个键被按下，该功能需要在系统偏好设置的 "Accessibiliy" 中选中 "Enable acess for assistive devices" 选项。

你可以使用 **key code** 和 **keystroke** 在 AppleScript 中处理一些快捷键，这在很多时候比常规的脚本更有优势。事实上只要提到 iTunes, 如果不用 **key code** 和 **keystroke** 的话，部分快捷操作在 AppleScript 中是实现不了的。比如打开一个显示简介窗口或显示选项窗口。 接下来我们实验一下 **key code** 和 **keystroke** 命令在 AppleScript 中如何控制 iTunes.

<!-- more -->

### 系统偏好设置
为了在 "System Events" 中使用 **key code** 与 **keystroke** 命令，你必须开启 Mac OSX 的系统偏好设置中开启 accessibility 权限。

**Mavericks (10.9)和以后的系统.** Accessibiliy的权限必须基于每个应用来设置。 看苹果的[支持文档](http://support.apple.com/kb/HT5914)了解更多内容。

**Mavericks (10.9)以前的系统** : 点击系统偏好设置中的 "Accessibility" 模块(原来叫"Universal Access")。在底部左边获取选中"Enable access for assistive devices"。确保复选框被选中，关闭系统偏好设置。

### 实现 AppleScript 
"System Events" 会发送 **key code** 或 **keystroke** 给当前在前台活动的程序。所以你要通过 `activate`命令来保证 iTunes 处理于前台活动状态。为了易于大家理解，下面给出一个简单的使用示例。它将选中当前播放的 iTunes 音乐使用 **keystroke** 命令：

```
tell application "iTunes"
    activate
    if player state is not stopped then
        tell application "System Events" to keystroke "l" using command down
    end if
end tell
```

我已经模拟了一个键盘按下操作 "Command-L"(这个脚本确保 iTunes 不会被提前停止) 通过提供一个小写的"l"，就是正常你需要按下的键。 **using** 关键字代表这个 **keystroke** 这个命令是包含 Command 键按下的组合键。下面是使用 **key code** 的实现：

```
tell application "iTunes"
    activate
    if player state is not stopped then
        tell application "System Events" to key code 37 using command down
    end if
end tell
```

在上面的例子中， `37`这个数字代表小写的"l"。**using** 关键字的作用与 **keystroke** 例子中的作用是一样的： 代表 Command 被同时按下的组合键。

除此之外，你还可以使用 **using** 关键字包含多个键的组合。下面的脚本分别使用 **key code** 和 **keystroke** 两种方式实现在 iTunes 的浏览器中打开 iTunes Store (Command-Shift-H)的功能。 注意 **using** 关键字中多个键组合的使用：

**keystroke**:
```
tell application "System Events"
    tell application "iTunes" to activate
    keystroke "h" using {command down, shift down}
end tell
```

**key code**:
```
tell application "System Events"
    tell application "iTunes" to activate
    key code 4 using {command down, shift down}
end tell
```

数字`4`相当于"h"键。（想了解更多关于编写 **keystroke** 或 **key code** 命令的方法，在 AppleScript 编辑器中，Window > Library 中查看 "System Events"的信息）

### Go Nuts（让我们更进一步）
下面的脚本使用 **key code** 和 "正常的" AppleScript 语法来选择音乐库并且激活搜索框:

```
tell application "iTunes"
    activate
    -- select the Music library
    reveal (some playlist whose special kind is Music)
end tell
tell application "System Events"
    -- bring focus to Search box - Command-Option-F
    key code 3 using {command down, option down}
end tell
```

一个类似的版本，将剪贴板中的文字复制到搜索框中：

```
tell application "iTunes"
    activate
    reveal (some playlist whose special kind is Music)
end tell
tell application "System Events"
    -- bring focus to Search box - Command-Option-F
    key code 3 using {command down, option down}
    -- paste the clipboard - Command-V
    key code 9 using command down
end tell
```

选中播放列表中的所有音乐并且新建一个名叫 "untitled" 的播放列表包含所有音乐：

```
tell application "iTunes"
    activate
end tell
tell application "System Events"
    -- select all tracks - Command-A
    key code 0 using command down
    --new playlist from selection- Command-Shift-N
    key code 45 using {command down, shift down}
end tell
```

学会了么？

当然了，上面的脚本都可以用"正常"的方式来解决。我不是使用图形脚本的深度使用者，但是有时这是 iTunes 中执行某些任务的唯一方案。

你可能需要通过使用这些脚本来执行[快捷键](http://dougscripts.com/itunes/itinfo/shortcutkeys.php)任务。比如， 我已经把 "Music library & search" 映射到 Command-Option-S。

### 如何获得 **Key Codes**
使用 **keystroke** 命令非常简单直接：直接使用键值字符即可。为了取得一个键按下对应的 **key code**，我使用 Denis Bajram 写的免费的程序 [Full Key Codes](http://softwares.bajram.com/utilities/#Full_Key_Codes)，非常简单易用。它能显示键盘上某些键被按下时的数字、ASCII和十六进制数。

### 通用 **key code** 命令

```
-- select all tracks of front playlist - Command-A
tell application "System Events"
    tell application "iTunes" to activate
    key code 0 using command down
end tell

-- toggle browse mode - Command-B
tell application "System Events"
    tell application "iTunes" to activate
    key code 11 using command down
end tell

-- copy selection (or whatever) - Command-C
tell application "System Events"
    tell application "iTunes" to activate
    key code 8 using command down
end tell

-- toggle Right Sidebar - Shift-Command-G
tell application "System Events"
    tell application "iTunes" to activate
    key code 5 using {command down, shift down}
end tell

-- Get Info for selected track(s) - Command-I
tell application "System Events"
    tell application "iTunes" to activate
    key code 34 using command down
end tell

-- open View options window - Command-J
tell application "System Events"
    tell application "iTunes" to activate
    key code 38 using command down
end tell

-- select current track - Command-L
tell application "System Events"
    tell application "iTunes" to activate
    key code 37 using command down
end tell

-- Show File of selected - Shift-Command-R
tell application "System Events"
    tell application "iTunes" to activate
    key code 15 using {command down, shift down}
end tell

-- toggle Zoom of Main Browser - Control-Command-Z
tell application "System Events"
    tell application "iTunes" to activate
    key code 6 using {command down, control down}
end tell

-- Close a window with "Cancel" - Escape key
tell application "System Events"
    tell application "iTunes" to activate
    key code 53
end tell


(* 
THE FOLLOWING ARE DELETE ACTIONS!
USE CAREFULLY!!!
*)
-- Delete the selected playlist from your Source list without confirming that you want to delete it - Command-Delete
tell application "System Events"
    tell application "iTunes" to activate
    key code 51 using command down
end tell

-- Delete the selected playlist and all the songs it contains from your library 
-- OR
-- Delete the selected song from your library and all playlists
-- Option-Delete
tell application "System Events"
    tell application "iTunes" to activate
    key code 51 using option down
end tell

-- Select the search field - Command-Option-F
tell application "System Events"
    tell application "iTunes" to activate
    key code 3 using {command down, option down}
    -- and optionally, paste the clipboard - Command-V
    
    -- key code 9 using command down
    
end tell


-- playlist and folder

-- new playlist - Command-N
tell application "System Events"
    tell application "iTunes" to activate
    key code 45 using {command down, shift down}
end tell

-- new playlist from selection - Command-Shift-N
tell application "System Events"
    tell application "iTunes" to activate
    key code 45 using {command down, shift down}
end tell

-- new Smart Playlist - Command-Option-N
tell application "System Events"
    tell application "iTunes" to activate
    key code 45 using {command down, option down}
end tell

-- new Playlist Folder - Command-Option-Shift-N
tell application "System Events"
    tell application "iTunes" to activate
    key code 45 using {command down, option down, shift down}
end tell

-- toggle Show/Hide Main Browser window - Command-1
tell application "System Events"
    tell application "iTunes" to activate
    key code 18 using command down
end tell

-- toggle Show/Hide Equalizer window - Command-2
tell application "System Events"
    tell application "iTunes" to activate
    key code 19 using command down
end tell
```

原文:<http://dougscripts.com/itunes/itinfo/keycodes.php>