---
layout: post
title: "storm-0.9.5-released"
date: 2015-06-16 20:55:25
tags: 
		- storm
---
此版本更新于2015年6月4日
Apache Storm社区和很高兴的宣布0.9.5版本已经发布，可以从[下载页面](https://storm.apache.org/downloads.html)下载了。
这是一个维护发布，包括一些重要bug的修复，改变Storm的稳定性和容错能力。我们鼓励使用以前版本的使用者更新到最新版本。
### 感谢
特别感谢这些为Apache Storm作出贡献的人，通过直接的代码提交，文档，报告bug或者在邮件列表里帮助其他使用者。感谢你的努力。
<!-- more -->
### 所有的更新日志
> * 输出日志"task is null"取代让worker死掉，当任务在transfer-fn是空的
> * 在ShellSpout中增加"error"命令支持
> * 修复storm.cmd中去正确的模拟"shift"去运行'storm jar'
> * 修复Supervisor被挂掉由于 java.io.FileNotFoundException: File '../stormconf.ser' does not exist.