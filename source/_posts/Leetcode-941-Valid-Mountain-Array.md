---
layout: post
title: 'Leetcode 941: Valid Mountain Array'
categories: 算法
date: 2018-11-19 09:30:11
tags:
    - Leetcode
    - 941
    - Python3
---
**难度：Easy**
### 题目描述
给定整数数组A，如果数组是一个有效的山型数组则返回true。

A是一个山型数组当且仅当：
> * A.length >= 3
> * 存在i(0 < i < A.length -1)，以至于
    * A[0] < A[1] < ... A[i-1] < A[i]
    * A[i] > A[i+1] > ... > A[B.length - 1]

<!--more-->
### 示例
**示例1**
```shell
输入: [2,1]
输出: false
```
**示例2**
```shell
输入: [3,5,5]
输出: false
```
**示例3**
```shell
输入: [0,3,2,1]
输出: true
```

### 提示
> * 0 <= A.length <= 10000
> * 0 <= A[i] <= 10000 

### 解题思路
题目中山型数组的意思是，数组长度至少为3，且数组要严格的先递增再递减，理解这个后根据题意即可写出代码，详细的参考下面的代码。

### 解题代码
**Python3**
```python
class Solution:
    def validMountainArray(self, A):
        """
        :type A: List[int]
        :rtype: bool
        """
        if len(A) < 3:
            return False
        index = 1
        while index < len(A):
            if A[index] <= A[index-1]:
                index -= 1
                break
            index += 1
        if index == 0 or index == len(A):
            return False
        while index < len(A)-1:
            if A[index+1] >= A[index]:
                return False
            index += 1
        return True
```
