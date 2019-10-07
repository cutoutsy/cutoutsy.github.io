---
layout: post
title: MNIST数据集介绍
categories: 算法
date: 2018-07-13 17:18:49
tags:
    - 机器学习
    - 数据集
---
如果你在学习机器学习，那么你肯定对MNIST数据集不陌生，很多机器学习例子以及很多人都拿MNIST当做例子。
MNIST是手写数字图片数据集，包含60000张训练样本和10000张测试样本。

![MNIST](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_mnist.png)

<!-- more -->

MNIST数据集来自美国国家标准与技术研究所，National Institute of Standards and Technology(NIST)。现在你是不是知道这个数据集名字的由来了。M是Modified的缩写。训练集是由来自250个不同人手写的数字构成，其中50%是高中学生，50%来自人口普查局的工作人员。测试集也是同样比例的手写数字数据。每张图片有28x28个像素点构成，每个像素点用一个灰度值表示，这里是将28x28的像素展开为一个一维的行向量（每行784个值）。图片标签为one-hot编码：0-9。

MNIST数据集可在[http://yann.lecun.com/exdb/mnist/](http://yann.lecun.com/exdb/mnist/)处获取，包含4个部分
- train-images-idx3-ubyte.gz:  training set images (9912422 bytes) 
- train-labels-idx1-ubyte.gz:  training set labels (28881 bytes) 
- t10k-images-idx3-ubyte.gz:   test set images (1648877 bytes) 
- t10k-labels-idx1-ubyte.gz:   test set labels (4542 bytes)

### 使用
通过TensorFlow来使用，代码如下
```Python
from tensorflow.examples.tutorials.mnist import input_data
mnist = input_data.read_data_sets("MNIST_data", one_hot=True)
```
这个代码将会把数据下载到当前目录下的MNIST_data目录下，如果你想下载到其他目录，可以更改这个参数。当然你也可以提前下载好，然后放在某个目录，这样上面的代码就不会去下载了。
```Python
X_train = mnist.train.images    # (55000, 784)
Y_train = mnist.train.labels    # (55000, 10)
X_test = mnist.test.images      # (10000, 784)
Y_test = mnist.test.labels      # (10000, 10)

batch_X, batch_Y = mnist.train.next_batch(64)
```