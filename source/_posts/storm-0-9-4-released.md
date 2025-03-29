---
layout: post
title: "storm 0.9.4 released"
date: 2015-05-08 00:14:32
tags: 
		- storm
		- bigdata
---
此版本在3月25日更新

这是一个维护版本，包括一些重要的bug的修复和改善storm的稳定性和容错性。鼓励先前版本的用户升级到这个最新版本
<!--more -->
### 完整的更新

> * 在README里把2181作为ZkHosts的端口
> * supervisor应该优雅的处理挂掉的worker
> * 当kafka写消息失败时，应该报告错误而不是静静的ack
> * 通过改善重连策略和缓冲消息来修复Strom级联失败
> * supervisor被kill掉由于java.io.FileNotFoundException: File '../stormconf.ser' does not exist
