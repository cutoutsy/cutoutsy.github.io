---
layout: post
title: SVN搭建及配置
categories: 工作
date: 2021-08-12 20:54:00
tags:
    - SVN
    - 版本控制
    - 搭建 
---

### 0x01 SVN简介
SVN知道的同学可能比较少，但是大家可能对git都不陌生，相信大家基本都用过全球最大同性交友网站github。

SVN和git是一样的，都是一种版本控制系统，用于多个人共同开发同一个项目，共用资源的目的。

SVN历史比较悠久，在git出来前就有。

### 0x02 SVN服务器
SVN是CS模式，需要有服务器。SVN服务器支持两种运行方式：独立服务器和借助apache运行，本文主要讲独立服务器的运行方式

本文主要讲在Linux系统Ubuntu 18.04下的安装，其他发行版相差不大。

1. 安装SVN
```shell
apt-get install subversion
```
2. 建立SVN服务器的文件夹
```shell
# svn默认目录
mkdir /var/svn
```

<!--more-->

3. 在SVN文件夹下，创建SVN库文件夹
```shell
mkdir /var/svn/test
```
4. 创建库
SVN库文件夹一般需要在服务器创建
```shell
svnadmin create /var/svn/test
```
5. 查看是否创建成功
如果有以下文件，则创建成功

![svn repo file](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_svn_01.png)

### 0x03 SVN配置
创建库后，我们需要对库进行配置

主要在conf文件下，

![svn repo conf](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_svn_02.png)

1. 设置仓库用户和密码

```shell
vim passwd
```
在[users]块中添加用户和密码，格式: 账号=密码，如test=123

![svn conf passwd](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_svn_03.png)

2. 配置用户权限
```shell
vim authz
```
支持配置单个用户的权限和用户组的权限，这里我配置了用户组group1=test，然后赋予group1读写权限

![svn conf authz](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_svn_04.png)

3. svnserver配置
```shell
vim svnserver.conf
```
去掉下面4个配置项的注释
```shell
anon-access = read   # 匿名用户可读
auth-access = write  # 授权用户可写
password-db = passwd # 使用哪个文件作为账号文件
authz-db = authz     # 使用哪个文件作为权限文件
```

### 0x04 SVN服务启动和停止
1. 启动SVN
```shell
svnserve -d -r /var/svn
```
2. 查看svn运行状态

可以通过ps -ef | grep svn来查看SVN服务是否运行
```shell
ps -ef | grep svn
```

也可以通过端口查看，SVN服务器默认开发端口3690

```shell
netstat -lnp | grep 3690
```

3. 停止SVN服务

可以先通过ps -ef | grep svn 找到SVN进程ID，然后kill -9 pid

### 0x05 SVN仓库文件管理

搭建好了之后，我上传了一个文件，在服务器端找个好久，都没有找到对应的文件。查了一下，发现

SVN服务器端不是简单将上传的文件一个一个存放起来。

SVN服务器端默认采用的FSFS格式，是将每次commit的内容增量方式存放的，每个增量包存成一个文件，这个增量包中包括了这次commit的全部数据。

也就是说在服务器端存放该版本库的文件夹下不可能找到某个文件。

SVN服务器版本库主要有两种格式，一种为FSFS，另一种为BDB。

把文件上传SVN版本库后，上传的文件不再以文件原来的格式存储，而是被SVN以他自定义的格式压缩成版本库数据，存放在版本库中。

如果是FSFS格式，这些数据存放在版本库的db目录中，里面的revs和revprops分别存放这每次提交的差异数据和日志等信息。


### 0x06 参考资料

https://www.jianshu.com/p/a247ccfba7fe

https://blog.csdn.net/qq_29945729/article/details/52936900
