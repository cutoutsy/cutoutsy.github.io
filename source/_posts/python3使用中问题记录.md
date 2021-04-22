---
layout: post
title: python3使用中问题记录
categories: 学习笔记
date: 2018-08-29 15:16:08
tags:
    - Python3
    - 问题记录
---
### 写在前面
本篇博客为博主在使用python3中遇到的问题以及需要记录的东西，新手在使用中有较多的问题，在这做个记录，方便查询和回顾，持续更新中...
<!--more-->

### eval使用
之前没有使用过eval函数，今天在代码中看到一个使用方法是传入一个字符串形式类似为:['a.txt', 'b.txt']，将之转化为一个list，方便后面调用。
可以使用下面的代码运行函数，将函数名字符串结合上参数列表即可
```python
eval(func + "(df, wordList, cache)")
```


更详细的应用：[https://blog.csdn.net/qq_29883591/article/details/53443062](https://blog.csdn.net/qq_29883591/article/details/53443062)

### python中的文件路径问题
获取当前所处文件夹的绝对路径
```python
os.path.abspath('.')  
os.path.abspath('..')  #表示当前所处的文件夹上一级文件夹的绝对路径
```
一个python源码在运行时有个解释器自带的参数__file__，这个量会告诉你当前这个代码的地址
```python
module_path = os.path.dirname(__file__)
```

### python数据读取
这个是在OJ的时候，如何读取题目的输入
```python
import sys
line = sys.stdin.readline() # 每次读取一行
line = input() # 使用input()也可以每次读取一行
```

### 多进程与多进程
多进程主要是使用multiprocessing模块，封装了系统的fork()方法，并达到平台移植的目的。
多线程主要使用threading模块，但是python解释器由于设计时有GIL全局锁，导致了多线程无法利用多核。所以python并不存在真正意义上的多线程。多线程在python中只能交替的执行。
可以参考[廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014319272686365ec7ceaeca33428c914edf8f70cca383000)

### 嵌套函数
在Python中，函数是一个一等公民。这意味着，函数与任何其他对象（例如：整数、字符串、列表）一致，既可以动态地创建或销毁，也可以传递给其他函数，或作为值进行返回。
一般在什么情况下使用嵌套函数：
* 封装-数据隐藏
* 贯彻DRY(Don't Repeat Yourself)原则: 指在程序设计以及计算中避免重复代码，因为这样会降低灵活性、简洁性，并且有可能导致代码之间的矛盾。
* 闭包
