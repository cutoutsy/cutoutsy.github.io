---
layout: post
title: Centos7下支持挂载exFAT文件系统
categories: 杂记
date: 2018-07-16 14:49:53
tags:
    - Centos
    - exFAT
    - 挂载
---
### 写在前面
我的电脑是乞丐版Mac，内存只有128G，所以经常有空间不足的情况，今天在解压一个大文件时，系统就一直警告存储即将满了，所以就考虑将文件通过U盘拷贝到台式机上操作。拷贝完后插在台式机上并没有自动识别，经过搜索，发现Centos7是默认不支持exFAT文件系统的。遂有了这篇博客...

<!-- more -->

### 解决方案
在Windows下常见的文件系统有FAT、FAT32、NTFS和exFAT，由于FAT和FAT32不支持超过4G以上的文件，现在好多文件大小都会超过4G，而Mac又不能读取NTFS，所以我的U盘格式一般都是exFAT。

通过查询，exFAT格式的文件系统是通过fuse模块来支持的，解决方案如下：
```shell
yum install -y http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-5.el7.nux.noarch.rpm
yum install exfat-utils fuse-exfat
```
然后再执行挂载操作即可。
```shell
cd /media
mkdir udisk
mount -t exfat /dev/sdbX(X是你的盘号） udisk
```