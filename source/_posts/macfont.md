---
layout: post
title: Mac还原系统默认字体
date: 2015-10-30 12:04:35
tags:
	- Mac
	- font
---
### 0x01写在前面
今天看了一天的电视剧伪装者，在我看过的谍战片里面算不错的啦，各种伪装，看的有点乱。

用Mac以来一直就用的系统的默认字体，感觉还不错。前两天装了office for mac 2011，

![macfont](http://7oxhal.com1.z0.glb.clouddn.com/macfont.png)
<!-- more -->
再打开网页的时候发现有的中文字体改变了，变成黑体，我就看着不舒服了，于是想改成Mac默认的字体。
### 0x02 还原默认字体
还原默认字体，我们要用到Mac自带的字体册，字体册自带重置功能，可以让用户快速简单进行恢复工作。
具体方法：打开「字体册」，选择菜单栏「文件」中的「恢复标准字体」。这个菜单栏选项将Mac电脑的字体重置为默认的标准字体，同时把所有第三方字体移动至一个叫 Fonts(Removed) 的文件夹。在这过程中，你有充足的时间输入账户密码，完成后点击「重置」即可。

字体已经被恢复了，但还差了几步。首先，在以下路径中查看被删除的字体列表：~/Library/Fonts (Removed)，你可以在 Finder 作为焦点窗口时，使用组合键⌘⇧G调取路径输入框。最后，把需要安装的字体拖拽至「字体册」即可进行安装。

关于更多的Mac字体的设置，大家可以看看字体册的使用。
