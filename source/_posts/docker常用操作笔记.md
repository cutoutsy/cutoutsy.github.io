---
layout: post
title: docker常用操作笔记
categories: 笔记
date: 2019-10-08 11:03:58
tags:
    - docker
    - linux
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

### 列出所有容器
```shell
docker ps -a
```

### 停止所有容器
```shell
docker stop $(docker ps -aq)
```

持续更新中...
