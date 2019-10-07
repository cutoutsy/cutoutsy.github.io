---
layout: post
title: 'Leetcode 925: Long Pressed Name'
categories: 算法
date: 2018-11-22 10:11:21
tags:
    - Leetcode
    - 925
    - Python3
---
**难度：Easy**
### 题目描述
你的朋友正在使用键盘输入他的名字name。偶尔，在键入字符c时，按键可能会被长按，而字符可能被输入1次或多次。

你将会检查键盘输入的字符typed。如果它对应的可能是你的朋友的名字（其中一些字符可能被长按），那么就返回True。

### 示例
**示例1**
```shell
输入：name = "alex", typed = "aaleex"
输出：true
解释：'alex' 中的 'a' 和 'e' 被长按。
```
<!--more-->
**示例2**
```shell
输入：name = "saeed", typed = "ssaaedd"
输出：false
解释：'e' 一定需要被键入两次，但在typed的输出中不是这样。
```
**示例3**
```shell
输入：name = "leelee", typed = "lleeelee"
输出：true
```
**示例4**
```shell
输入：name = "laiden", typed = "laiden"
输出：true
解释：长按名字中的字符并不是必要的。
```

### 提示
> * name.length <= 1000
> * typed.length <= 1000
> * name和typed的字符都是小写字母。

### 解题思路
这个题目是对两个字符串进行操作，很容易想到双指针，如果name[i]和typed[j]相等，那么就都往下走一步，当不相等时，由于长按，如果typed[j] == typed[j-1]，可以让j++。
最后i要等于name.length。

具体参考下面的代码。

### 解题代码
**Python3**
```python
class Solution:
    def isLongPressedName(self, name, typed):
        """
        :type name: str
        :type typed: str
        :rtype: bool
        """
        idx = 0
        for i in range(len(typed)):
            if idx < len(name) and name[idx] == typed[i]:
                idx += 1
            elif i == 0 or typed[i] != typed[i-1]:
                return False
        return idx == len(name)
```