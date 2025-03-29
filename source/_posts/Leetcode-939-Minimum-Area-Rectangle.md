---
layout: post
title: 'Leetcode 939: Minimum Area Rectangle'
categories: 算法
date: 2018-11-15 11:13:45
tags:
    - Leetcode
    - 939
    - Python3
---
**难度：Medium**
### 题目描述
给定xy平面的一些点的集合，找出这些点能组成的最小面积的矩形，矩形的边要平行于x，y轴。

如果不能组成矩形，返回0.

### 示例
**示例1**
```shell
输入: [[1,1],[1,3],[3,1],[3,3],[2,2]]
输出: 4
```
<!--more-->
**示例2**
```shell
输入: [[1,1],[1,3],[3,1],[3,3],[4,1],[4,3]]
输出: 2
```

### 注意
> * 1 <= points.length <= 500
> * 0 <= points[i][0] <= 40000
> * 0 <= points[i][1] <= 40000
> * 所有点都不相同

### 解题思路
主要还是从题目中要构成矩形的点的特征出发，题目要求矩形的边要和x，y轴平行，那么如果x = $x_{0}$有多个点，x=$x_{1}$也有多个点，那么能构成矩形的是$x=x_{0}, x=x_{1}$这些点的y坐标的交集，才能满足题目的要求。

比如在示例1中，x = 1的点有两个[1, 1], [1, 3]，x = 3的点有两个[3, 1], [3, 3]，那么共同的y坐标有1， 3，那么面积可以由x的差值乘以y的差值计算得出。

可以利用hashmap来存储具有共同x坐标的y，然后对不同x的y坐标求交集，得到最小的面积即可。具体见下面的代码。

### 解题代码
**Python3**
```python
class Solution:
    def minAreaRect(self, points):
        """
        :type points: List[List[int]]
        :rtype: int
        """
        pdict = collections.defaultdict(list)
        for x, y in points:
            pdict[x].append(y)
        keys = [key for key in pdict.keys() if len(pdict[key]) >= 2]
        minArea = sys.maxsize
        for i in range(0, len(keys)):
            for j in range(i+1, len(keys)):
                y = sorted([e1 for e1 in pdict[keys[i]] if e1 in pdict[keys[j]]])
                if len(y) >= 2:
                    miny = sys.maxsize
                    for k in range(1, len(y)):
                        miny = min(miny, y[k]-y[k-1])
                    minArea = min(minArea, abs(keys[i]-keys[j]) * miny)
        return 0 if minArea == sys.maxsize else minArea
```

