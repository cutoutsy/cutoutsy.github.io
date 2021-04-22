---
layout: post
title: 'Leetcode 937: Reorder Log Files'
categories: 算法
date: 2018-11-12 15:49:38
tags:
    - Leetcode
    - Python3
    - 937
---
**难度：Easy**
### 题目描述
给你一个数组logs,每个log都是由空格分开的单词组成的。

对于每个log，第一个单词是字母数字的标识符。然后，要么
> * 在标识符后面的每个单词仅由小写字母组成，或者
> * 在标识符后面的每个单词仅由数字组成

我们称这两种日志种类分别为字符日志和数字日志。每个日志保证在标识符后面会有一个单词。

对日志重新排序，让字符日志在数字日志前面，字符日志忽略标识符按照字母顺序排序，数字日志按照原来的顺序排。

返回最终的日志顺序。

<!--more-->

### 示例
```shell
输入: ["a1 9 2 3 1","g1 act car","zo4 4 7","ab1 off key dog","a8 act zoo"]
输出: ["g1 act car","a8 act zoo","ab1 off key dog","a1 9 2 3 1","zo4 4 7"]
```

### 注意
> * 0 <= logs.length <= 100
> * 3 <= logs[i].length <= 100
> * logs[i]保证有一个标识符，并且标识符后面有一个单词

### 解题思路
根据题意，我们需要先将原日志数组分为字符日志和数字日志，然后再对字符日志进行排序。

字符日志的排序可以将除标识符后的单词组合起来进行排序，即可达到按字母顺序排序的要求。

### 解题代码
```python
class Solution:
    def reorderLogFiles(self, logs):
        """
        :type logs: List[str]
        :rtype: List[str]
        """
        str_list = [x for x in logs if not str.isdigit(x.split()[1])]
        num_list = [x for x in logs if str.isdigit(x.split()[1])]
        order_str = sorted(str_list, key=lambda x: " ".join(x.split()[1:]))
        return order_str + num_list
```
