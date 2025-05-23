---
layout: post
title: 'Linux学习:lsattr、chattr命令'
date: 2017-04-10 09:12:54
tags:
		- Linux
		- lsattr
		- chattr
---
前两天在服务器上发现了几个异常文件，应该不是自己创建的，感觉服务器被人攻击了。然后我在删除这几个文件的时候，说我没有权限，黑人问号，我可是root用户，还没有权限删除这几个文件么。

上网上查了下，应该是用chattr命令锁定文件了。然后用lsattr命令查看了下，果然是这样的

<!--more-->

```shell
[root@localhost ~]# lsattr linux 
----i--------e-- linux
[root@localhost ~]# 
```

其中i表示该文件不能被删除、改名、设定链接关系，同时不能写入或新增内容。

下面就来详细学习下lsattr和chattr命令，之前没有使用过。

### chattr命令

作用：chattr命令主要是改变文件、目录属性的，与chmod命令相比，chmod只是改变文件的读写、执行权限，更底层的属性控制由chattr来改变。

用法：chattr [ -RVf ] [ -v version ] [ mode ] files...

-R 表示递归改变整个目录下的所有文件属性
-V 输出详细信息

主要是mode部分，[mode]部分由+-=和[ASacDdIiJsTtu]这些字符组成，这部分才是用来控制文件的属性的。

\+ ：在原有参数设定基础上，追加参数。
\- ：在原有参数设定基础上，移除参数。
= ：更新为指定参数设定。
A：文件或目录的 atime (access time)不可被修改(modified), 可以有效预防例如手提电脑磁盘I/O错误的发生。
S：硬盘I/O同步选项，功能类似sync。
a：即append，设定该参数后，只能向文件中添加数据，而不能删除，多用于服务器日志文件安全，只有root才能设定这个属性。
c：即compresse，设定文件是否经压缩后再存储。读取时需要经过自动解压操作。
d：即no dump，设定文件不能成为dump程序的备份目标。
i：设定文件不能被删除、改名、设定链接关系，同时不能写入或新增内容。i参数对于文件 系统的安全设置有很大帮助。
j：即journal，设定此参数使得当通过mount参数：data=ordered或者data=writeback挂载的文件系统，文件在写入时会先被记录(在journal中)。如果filesystem被设定参数为data=journal，则该参数自动失效。
s：保密性地删除文件或目录，即硬盘空间被全部收回。
u：与s相反，当设定为u时，数据内容其实还存在磁盘中，可以用于undeletion。

各参数选项中常用到的是a和i。a选项强制只可添加不可删除，多用于日志系统的安全设定。而i是更为严格的安全设定，只有superuser (root) 或具有CAP_LINUX_IMMUTABLE处理能力（标识）的进程能够施加该选项。

我们刚通过lsattr看到的i选项就是这儿添加的i文件属性控制。

可以通过chattr命令防止系统中某个关键文件被修改，也可以给日志系统设置a属性，只能向日志文件添加数据。

### lsattr命令

作用：显示文件或者目录的属性

用法：lsattr [ -RVadv ] [ files...  ]

-R  显示目录及其里面的文件和目录的属性
-a  显示所有的文件和目录，包括以"."开头的隐藏文件

### 简单示例

```shell

[root@localhost ~]# lsattr /etc/resolv.conf 
-------------e-- /etc/resolv.conf
[root@localhost ~]# 
[root@localhost ~]# chattr +i /etc/resolv.conf 
[root@localhost ~]# 
[root@localhost ~]# lsattr /etc/resolv.conf 
----i--------e-- /etc/resolv.conf
[root@localhost ~]# 
[root@localhost ~]# rm -rf /etc/resolv.conf 
rm: cannot remove ‘/etc/resolv.conf’: Operation not permitted

```










