---
layout: post
title: 炼丹炉：TensorFlow简介
categories: 深度学习
date: 2018-07-12 16:48:27
tags:
    - 深度学习
    - Google
    - TensorFlow
---
### 写在前面
最近准备进入炼丹行列，话说工欲善其事，必先利其器，需要寻找一个好的丹炉。之前就有听说过TensorFlow以及一些其他的工具，最后决定好好学习了解下TensorFlow，毕竟这是Google出品。
从名字来看，可以拆成两部分，Tensor含义是张量，张量是线性代数中的知识，指一个数组中的元素分布在超过二维的空间中，称之为张量。比如图像数据，基本都是二维以上的。FLow含义是流动。所以TensofFlow的意思就是
<!-- more -->
数据张量流动。好像意思不太对。其实TensorFlow是采用计算图的方式，那么含义就是数据张量在计算图中流动，计算图我们可以理解为神经网络图，这样好像就说得通了。

### TensorFlow安装(CPU)
安装分为两种，CPU安装和GPU安装，CPU安装很简单，直接使用pip即可
```shell
pip install tensorflow
pip install --upgrade tensorflow    # 升级tensorflow
```
<!-- GPU安装的坑比较多，需要预先安装英伟达的cuda，如果大家要安装GPU版本，可以自行搜索下。这儿我们先安装一个CPU版本供学习使用。 -->

### Hello World
万年不边的先输出“Hello World”，同时验证下安装是否成功。
打开python解释器，输入下面的python代码。
```python
import tensorflow as tf
hello = tf.constant("Hello, World!")
sess = tf.Session()
print(sess.run(hello))
```
输出
```shell
b'Hello, World!'
```
首先，通过tf.constant创建一个常量，然后启动TensorFlow的Session，调用sess的run方法来启动整个graph。

### TensorFlow安装(GPU)
- Win10安装
首先需要安装cuda，cuda的主要作用是链接GPU和应用程序，方便用户通过cuda的API调度GPU进行计算。
可以选择安装cuda10，下载地址[https://developer.nvidia.com/cuda-downloads](https://developer.nvidia.com/cuda-downloads)
一路选择默认安装就可以，安装好之后，打开cmd，出现下图所示的信息，则表示安装成功。
![nvcc](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_tensorflow_nvcc.png)

之后就可以执行tensorflow-gpu的安装
```shell
pip install tensorfow-gpu
```

### TensorFlow基础
TensorFlow是声明式编程，先构建计算图，再输入数值进行计算。先拿到计算图，便于优化。可视化计算图。方便存取。缺点是很多语言特性用不上，不易于调试且监视中间结果不简单。


### 参看文献
[Win10安装CUDA10和cuDNN](https://blog.csdn.net/qq_37296487/article/details/83028394)
