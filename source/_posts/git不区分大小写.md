---
layout: post
title: git不区分大小写
date: 2016-05-26 16:13:35
tags: 
		- git
		- 大小写
---
一直用git管理项目，在一个项目中，不小心把文件名首字母写成了大写。结果在服务器上编译不通过，报错指向文件名大写的文件，于是我在本地将文件名改成小写，提交的时候发现居然没有新的改动需要提交。

<!-- more -->

上网查了一番，发现git默认是不区分大小写的，即git默认对大小写不敏感。

简单记录两种解决办法：

1.配置git使其对文件名大小写敏感

在项目根目录下执行

```shell
git config core.ignorecase false
```

2.删除文件，然后重新创建提交

(1)删除文件

```shell
git rm A.java
```

(2)重新添加

```shell
git add a.java
```

(3)提交

```shell
git commit -m "a.java"
```

(4)提交到远程仓库

```shell
git push origin maste
```

推荐第一种方式，简单一些。