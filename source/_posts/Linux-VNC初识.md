---
layout: post
title: 'Linux:VNC初识'
date: 2016-12-08 08:36:12
tags:
		- Linux
		- VNC
---

最近在公司做安装测试，有一种方式是GUI安装，不过我们的Server都是类*unix无界面的，所以就需要用到VNC来进行界面的安装，本来服务器上都安装好了，直接使用就可以了，不过在使用的过程中遇到了问题，于是就了解了一下VNC，在此做个记录。

我们一般连接服务器是使用SSH，进行的是文字界面的连接。而VNC是图形界面的连接，要想达到这一目的，首先需要在服务器上安装VNC Server，以我使用的Centos 7.2为例，只需执行：

<!-- more -->

```shell
yum install tigervnc-server
```
安装就ok了。VNC Server会在服务器端监听一个端口，一般是在5901~5910之间，当用户端启动TightVNC Viewer连接到服务器开放的端口后，VNC Server将一堆预定设定好的X client透过这个端口发送到用户端上，最终就能在用户端显示服务器的图形界面了。

VNC Server一般不需要长期开启（像sshd似的），一般都是独立提供给一个用户来连接的，当你要使用VNC时，在连接到服务器上去，启动VNC server，手动启动，使用完毕后，再将VNC Server关闭即可。

下面看一下具体的操作：
在服务器上开启vncserver
![vncserver_start](http://7oxhal.com1.z0.glb.clouddn.com/vncserver_start.PNG)
我们将VNC Server的端口开在5902端口，要注意输入的密码至少需要6个字符。生成的密码会放在~/.vnc/passwd下，如果已经存在，则不会存在输入密码的界面。如果要修改密码的话直接使用vncpasswd就可以了。

在windows上使用TightVNC Viewer去连接：

![tightvnc_client](http://7oxhal.com1.z0.glb.clouddn.com/tightVNC_client.PNG)

输入服务器的地址（IP或者域名）,然后输入开启的端口，点击connect.输入你刚才设置的密码，就可以得到如下的界面：

![connect_success](http://7oxhal.com1.z0.glb.clouddn.com/connect_success.PNG)

在server上
```shell
setenv DISPLAY hostname/ip:2 或者
export DISPLAY=hostname/ip:2
```
然后在server打开gedit，就可以得到gedit的编辑界面：

![vnc_gedit](http://7oxhal.com1.z0.glb.clouddn.com/vnc_gedit.PNG)

当然你也可以打开其他一些需要界面的程序。

除此之外，vnc还可以这样使用，比如下面这种场景：
服务器A安装了VNC Server，服务器B没有安装VNC Server，但是服务器A，B互相可以ssh无密码登陆，这时如果我们想在服务器B上运行带界面的程序，然后通过TightVNC Viewer显示出来。

我们可以在服务器B上执行同样的命令：
```shell
setenv DISPLAY server A:2 或者
export DISPLAY=server A:2
```
然后用TightVNC Viewer连接服务器A及相应端口，在服务器B上执行需要界面的程序，在TightVNC Viewer上就可以显示出来。

下面说下我在使用时遇到的问题，我使用的场景是上面这种，当我执行需要界面的程序是，报错如下：
Xlib: connection to "tzedek:2.0" refused by server
Xlib: No protocol specified

(gedit:12646): Gtk-WARNING **: cannot open display:

后面通过查询发现，这很有可能是由于VNC auth的问题，可以把.Xauthority和passwd删除后，重新启动vncserver,重新输入密码就可以了。

关于VNC的了解差不多就这么多了，欢迎批评交流。
