---
layout: post
title: pandas常见一些用法总结
categories: 算法
date: 2018-09-06 10:26:49
tags:
    - pandas
    - 使用
---
### 写在前面
这篇博客是平时使用pandas的一些记录。

### 删除某列
```python
del df['column_name']
```

<!--more-->

### 滑动窗口
使用rolling函数，如果没有自己需要的统计函数，可以自己写，通过apply。函数传入的值是这个窗口内的值。
```python
rolling(window=3)
rolling(window=3).apply(func())
```
