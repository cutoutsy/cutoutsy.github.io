---
layout: post
title: 'Leetcode 942: DI String Match'
categories: 算法
date: 2018-11-20 10:08:32
tags:
    - Leetcode
    - 942
    - Python3
---
**难度：Easy**
### 题目描述
给定一个仅包含"I"(Increase)或者"D"(Decrease)的字符串S，让N = S.length。

返回[0, 1, ..., N]的任何排列，使得对于所有的i = 0, ..., N-1:
> * 如果 S[i] == "I", 那么 A[i] < A[i+1]
> * 如果 S[i] == "D", 那么 A[i] > A[i+1]

<!--more-->
### 示例
**示例1**
```shell
输入: "IDID"
输出: [0,4,1,3,2]
```
**示例2**
```shell
输入: "III"
输出: [0,1,2,3]
```
**示例3**
```shell
输入: "DDI"
输出: [3,2,0,1]
```

### 提示
> * 1 <= S.length <= 10000
> * S仅包含字符"I"或"D"

### 解题思路
给定一个由"I"或"D"组成的字符串，根据这个字符串生成排列，由于对排列没有具体的要求，那么我们可以根据题意来进行生成。如果当前字符是"I"，那么可知下一个数字要大于当前数字，那么我们可以令当前数字为最小值，然后将最小值加1，如果当前字符是"D"，那么可知下一个数字要小于当前数字，那么我们可以令当前数字为最大值，然后最大值减1，可以知道按照上述规则，在生成排列的过程中，每一步都是满足的。

具体参见下面的代码。

### 解题代码
**Python3**
```python
class Solution:
    def diStringMatch(self, S):
        """
        :type S: str
        :rtype: List[int]
        """
        ans = []
        left = 0
        right = len(S)
        for x in S:
            if x == 'I':
                ans.append(left)
                left += 1
            else:
                ans.append(right)
                right -= 1
        ans.append(left)
        return ans
```