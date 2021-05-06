---
layout: post
title: docker常用操作笔记
categories: 笔记
date: 2019-10-08 11:03:58
tags:
    - docker
    - Linux
---
### 镜像打包
```shell
docker build --network=host -t image_name .
```

### 启动镜像
```shell
docker run -i -t -d --name name --net=host -p 2334:2334 image_name
```

### 进入镜像
```shell
docker exec -it name bash
```
<!--more-->

### 容器与主机之间的数据拷贝
使用docker cp命令
```shell
docker cp CONTAINER:SRC_PATH DEST_PATH
docker cp DEST_PATH CONTAINER:SRC_PATH
```
将主机/www/test目录拷贝到容器test的/www目录下
```shell
docker cp /www/test test:/www
```

### 列出所有容器
```shell
docker ps -a
```

### 停止所有容器
```shell
docker stop $(docker ps -aq)
```

### docker启动机制
Docker的机制是让容器后台运行，必须至少有一个前台进程，容器运行的命令如果不是那些一直挂起的命令（比如运行top, tail）,会自动退出。

如果容器需要同时启动多个进程，只需要将其中一个挂起到前台即可。

持续更新中...
