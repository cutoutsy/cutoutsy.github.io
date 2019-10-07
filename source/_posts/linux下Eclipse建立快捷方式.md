---
layout: post
title: "linux下Eclipse建立快捷方式"
date: 2015-04-12 23:17:47
tags: 
		- java
		- eclipse
---
在linux下，Eclipse解压后直接就可以用了，但是每次都要去目录下启动，很不方便，我们可以考虑创建个快捷方式，并加入dock中，这样就方便很多了。
### 1、创建eclipse.desktop文件
```shell
sudo touch /usr/share/applications/eclipse.desktop
```
<!-- more -->
目录可能有的发行版不太一样，当然复制一份来修改是比较简单的
### 2、编辑eclipse.desktop文件
```shell
[Desktop Entry]
Name=Eclipse
Comment=Java IDE
Type=Application
Icon=/opt/Eclipse/eclipse/icon.xpm
Exec=/bin/sh "/opt/Eclipse/eclipse/eclipse.sh"
Categories=Application;Development
Terminal=false
MimeType=text/plain;
```
按照自己的Eclipse目录修改上面的eclipse.desktop
### 3、将eclipse加入dock中
如图:
![desktop](http://cutoutsy.qiniudn.com/desktop.png)


That's all....
