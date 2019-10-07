---
layout: post
title: CS224N Assignment 1
categories: 算法
date: 2018-07-14 16:22:24
tags:
    - 算法
    - nlp
    - CS224N
---
### 写在前面
这篇文章是斯坦福CS224N课程的课后作业1，先是进行大量的CBOW、负采样、交叉熵损失函数推导和证明，理论基础扎实后再过渡到实现，在斯坦福情感树上做情感分析、调参、分析混淆矩阵与错误。

公式中的向量是列向量，代码中的向量是行向量(效率更高)。

<!-- more -->

### Softmax
Softmax函数用于多分类过程中，它将多个神经元的输出，映射到(0, 1)区间内，可以看成概率来理解，从而进行多分类。
假设我们有一个数组V，$V_{i}$表示V中第i个元素，那么这个元素的softmax值就是：
$$S_{i} = \frac{e^i}{\sum_{j}{}e^j}$$
通过这个公式我们可以看到输出各个类别的概率是满足概率的性质的：
(1) 非负性：$0 \leq S_{i} \leq 1$
(2) 正则性公理：$\sum_{}{}S_{i} = 1$
所以在多分类的时候，我们选择值最大（即概率最大的）的类别作为我们预测目标。

#### 常数不变形
softmax不受输入的常数偏移影响，即
$$softmax(x) = softmax(x+c)$$
我们来推导下：
$$softmax(x + c)_{i} = \frac {e^{x_{i} + c}}{\sum_{j} e^{x_{j} + c}} = \frac {e^c\cdot e^{x_{i}}} {e^c\cdot \sum_{j} e^{x_{j}}} = \frac {e^{x_{i}}} {\sum_{j} e^{x_{j}}} = softmax(x)_{i}$$

#### python实现
利用上面的常数不变性质，我们来进行python实现
```Python
import numpy as np

def softmax(x):
    """ Compute the softmax function for each row of the input x.
    x -- A D dimensional vector or N x D dimensional numpy matrix

    Return:
    x -- modify x in-place
    """
    if len(x.shape) > 1:
        # Matrix
        x = x - x.max(1)[:, None]
        x = np.exp(x)
        x = x / x.sum(1)[:, None]
    else:
        # Vector
        x = x.max()
        x = np.exp(x)
        x = x/np.sum(x)
    
    return x
```
对于减掉一个最大值常量，是防止直接计算$e^x$出现数值溢出（容易）。

### 神经网络
#### sigmoid梯度推导
已知
$$\sigma(x) = \frac{1}{1+e^{-x}}$$
则：
$$\begin{align} \frac{d\sigma}{x} &= -\frac{1}{(1 + e^{-x})^2} \cdot (1+e^{-x})^{'}\\ &= \frac{e^{-x}}{(1 + e^{-x})^2}\\ &= \frac{1+ e^{-x} - 1}{(1 + e^{-x})^2}\\ &= \frac{1}{(1 + e^{-x})} - \frac{1}{(1 + e^{-x})^2}\\ &= \sigma(x) - \sigma(x)^{2}\\ &= \sigma(x)(1 - \sigma(x)) \end{align}$$

#### 交叉熵损失函数及梯度
交叉熵损失函数经常用于分类任务中，特别是在神经网络做分类问题时，经常使用交叉熵作为损失函数，由于交叉熵涉及到计算每个类别的概率，所以交叉熵几乎每次都和softmax函数一起出现。
预测使用$\hat{y} = softmax(\theta)$。交叉熵函数为：
$$CE(y, \hat{y}) = - \sum_{i}^{}{y_{i}log(\hat{y}_{i})}$$
