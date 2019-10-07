---
layout: post
title: Mac下安装破解版PS
categories: 杂记
date: 2017-07-11 09:19:46
tags:
	- Mac
	- photoshop
---
这段时间再帮人写一个网站前台，美工给的都是psd原型设计图。需要自己切图，这就不得不用到ps这个强大的软件了。最开始就准备装个破解版的，没有成功。就试用了30天，昨天到期了，不得不装个破解版的啦......

### 下载Adobe Photoshop CS6

百度或者google会出来很多的下载包，但是很多都不靠谱，今天下载了一个百度软件中心的，安装破解后中文字体显示发虚，而且软件启动界面也不是正版的样子。
<!--more-->
所以如果可以的话去[Adobe官网](https://helpx.adobe.com/cn/x-productkb/policy-pricing/cs6-CN-product-downloads.html)下载。
![ps下载](http://7oxhal.com1.z0.glb.clouddn.com/blog-psdownload.png)
选择适合自己的平台下载。也可以通过我分享的[百度云链接](http://pan.baidu.com/s/1o7wSTL4)进行下载，提取密码：sbv6，这个是从官网下载的。

### 安装Adobe Photoshop CS6

下载完成之后，双击解压，找到Install:

![ps安装](http://7oxhal.com1.z0.glb.clouddn.com/blog-psinstall.png)

我们安装试用版，在安装试用版之前请务必断网，点击Try/试用

![ps试用](http://7oxhal.com1.z0.glb.clouddn.com/blog-pstry.png)

接受协议，可能这之前会让你输入Adobe ID，选择稍后登录就可以了

![ps接受协议](http://7oxhal.com1.z0.glb.clouddn.com/blog-psacceptagreement.png)

之后就进入安装界面，等待安装结束就可以了。

### 破解Adobe Photoshop CS6
破解之前，你需要下载一个文件（amtlib.framework），这个放百度云盘地址可能会有问题，大家可以回复本文，我给大家下载百度云链接，这个亲测可用，第一次没有装上破解版就是因为没有下载到可用的这个文件。

下载amtlib.framework后，在“应用程序”文件夹，找到Adobe Photoshop CSS6文件夹，在里面找到Adobe Photoshop CS6.app，右键选择“显示包内容” 

![ps文件夹](http://7oxhal.com1.z0.glb.clouddn.com/blog-psshowpackage.png)

找到contents/framework文件夹，把刚才解压得到的amtlib.framework拷贝进去，替换整个文件夹,

![psframework](http://7oxhal.com1.z0.glb.clouddn.com/blog-psframework.png)

这儿有可能由于权限问题复制不进去，这个时候我们可以用shell在sudo下进行复制。

```shell
sudo cp -r amtlib.framework(你下载的amtlib.framework) /Applications/Adobe Photoshop CC/Adobe Photoshop CC.app/Contents/Frameworks
```

这样就破解结束了，破解成功的标志就是打开软件不会提示让你注册和试用天数倒计时，这是我破解后的ps(英文版)：

![ps ui](http://7oxhal.com1.z0.glb.clouddn.com/blog-psui.png)

这样妈妈再也不用担心我的ps试用期到期了......
