---
layout: post
title: CIFAR-10数据集介绍
categories: 算法
tags:
  - 机器学习
  - 数据集
date: 2019-06-11 11:13:10
---

### CIFAR-10
[官网](https://www.cs.toronto.edu/~kriz/cifar.html)
CIFAR-10是一个常见的图片分类数据集，有10类，共60000张32*32的彩色图片，所以每一类6000张。还有个更大的数据集CIFAR-100。

CIFAR-10数据集被分为5个训练的batch和一个测试的batch，我们来具体看下数据集。
下面显示的数据集的类，以及每个类随机的10张图片。

<!-- more -->

![CIFAR-10](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_cifar10.png)

官方给出了多个CIFAR-10数据集的版本，主要是Python版、Matlab版和二进制版。
> * [CIFAR-10 python version](https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz)
> * [CIFAR-10 Matlab version](https://www.cs.toronto.edu/~kriz/cifar-10-matlab.tar.gz)
> * [CIFAR-10 binary version (suitable for C programs)](https://www.cs.toronto.edu/~kriz/cifar-10-binary.tar.gz)

我们主要下载Python版本进行操作。

### 数据查看
```python
import _pickle as cPickle
import numpy as np
import os

CIFAR_DIR = "./cifar-10-batches-py"
print(os.listdir(CIFAR_DIR))

with open(os.path.join(CIFAR_DIR, "data_batch_1"), 'rb') as f:
    data = cPickle.load(f, encoding="latin1")
    print(type(data))
    print(data.keys())
    print(type(data['data']))
    print(type(data['labels']))
    print(type(data['batch_label']))
    print(type(data['filenames']))
    print(data['data'].shape)
    print(data['data'][0:2])
    print(data['labels'][0:2])
    print(data['batch_label'])
    print(data['filenames'][0:2])

image_arr = data['data'][100]
image_arr = image_arr.reshape((3, 32, 32))
# change channel position
image_arr = image_arr.transpose((1, 2, 0))

import matplotlib.pyplot as plt
from matplotlib.pyplot import imshow
%matplotlib inline

imshow(image_arr)
```

### 数据处理
```python
import tensorflow as tf
import os
import _pickle as cPickle
import numpy as np

CIFAR_DIR = "./cifar-10-batches-py"
print(os.listdir(CIFAR_DIR))
def load_data(filename):
    """read data from data file"""
    with open(filename, 'rb') as f:
        data = cPickle.load(f, encoding="latin1")
        return data['data'], data['labels']
# tensorflow.Dataset

class CifarData:
    def __init__(self, filenames, need_shuffle):
        all_data = []
        all_labels = []
        for filename in filenames:
            data, labels = load_data(filename)
            all_data.append(data)
            all_labels.append(labels)
        self._data = np.vstack(all_data)
        self._data = self._data / 127.5 - 1
        self._labels = np.hstack(all_labels)
        print(self._data.shape)
        print(self._labels.shape)
        self._num_examples = self._data.shape[0]
        self._need_shuffle = need_shuffle
        self._indicator = 0
        if self._need_shuffle:
            self._shuffle_data()
    
    def _shuffle_data(self):
        p = np.random.permutation(self._num_examples)
        self._data = self._data[p]
        self._labels = self._labels[p]
    
    def next_batch(self, batch_size):
        """return batch_size example as a batch."""
        end_indicator = self._indicator + batch_size
        if end_indicator > self._num_examples:
            if self._need_shuffle:
                self._shuffle_data()
                self._indicator = 0
                end_indicator = batch_size
            else:
                raise Exception("have no more examples")
        if end_indicator > self._num_examples:
            raise Exception("batch size is larger than all examples")
        batch_data = self._data[self._indicator: end_indicator]
        batch_labels = self._labels[self._indicator: end_indicator]
        self._indicator = end_indicator
        return batch_data, batch_labels

train_filenames = [os.path.join(CIFAR_DIR, 'data_batch_%d' % i) for i in range(1, 6)]
test_filenames = [os.path.join(CIFAR_DIR, 'test_batch')] 

train_data = CifarData(train_filenames, True)
test_data = CifarData(test_filenames, False)
```


