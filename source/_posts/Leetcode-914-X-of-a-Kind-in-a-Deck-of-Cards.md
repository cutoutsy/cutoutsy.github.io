---
layout: post
title: 'Leetcode 914: X of a Kind in a Deck of Cards'
categories: 算法
date: 2018-10-14 10:46:54
tags:
    - leetcode
    - 914
    - python3
---
**难度：Easy**
### 题目描述
在一副牌中，每张牌都写有一个整数。

返回true, 如果你可以选择X > 2以至于可以把整副牌切分成1个或者更多的组，满足：
* 每组都有X张牌
* 在每组中所有牌上的数字相同

<!--more-->

### 示例
**示例1**
```shell
输入: [1,2,3,4,4,3,2,1]
输出: true
解释: 可能划分 [1,1],[2,2],[3,3],[4,4]
```
**示例2**
```shell
输入: [1,1,1,2,2,2,3,3]
输出: false
解释: 不存在可能的划分
```
**示例3**
```shell
输入: [1]
输出: false
解释: 不存在可能的划分
```
**示例4**
```shell
输入: [1,1]
输出: true
解释: 可能划分 [1,1]
```
**示例5**
```shell
输入: [1,1,2,2,2,2]
输出: true
解释: 可能划分 [1,1],[2,2],[2,2]
```
### 注意
* 1 <= deck.length <= 10000
* 0 <= deck[i] < 10000

### 解题思路
由于题目中条件之一是每个组中的数字必须是相同的，所有我们应该对deck进行频数统计。

又由于每个组的大小都是一样的，那么X必定是所有频数的最大公约数。

基于上面的思路，可以写下如下的代码。

### 解题代码
**python3代码**
```python
from math import gcd
from collections import Counter

class Solution:
    def hasGroupsSizeX(self, deck):
        """
        :type deck: List[int]
        :rtype: bool
        """
        if len(deck) < 2:
            return False
        times = list(Counter(deck).values())
        if len(times) == 1:
            return True
        minv = gcd(times[0], times[1])
        if minv < 2:
            return False
        for ele in range(2, len(times)):
            minv = gcd(minv, times[ele])
            if minv < 2:
                return False
        return True
```