---
layout: post
title: 'Leetcode 884: Uncommon Words from Two Sentences'
categories: 算法
date: 2018-11-11 17:12:09
tags:
    - 算法
    - Leetcode
---
### 题目描述
给你两个句子A，B，每个句子是一个由单词组成的字符串，每个单词都是小写字母。

一个单词是uncommon，则表示它仅在一个句子出现一次，而在另一个句子中不出现。

返回所有的uncommon单词，可以以任何顺序。

<!--more-->

### 示例
#### 示例1
```shell
输入: A = "this apple is sweet", B = "this apple is sour"
输出: ["sweet","sour"]
```
#### 示例2
```shell
Input: A = "apple apple", B = "banana"
Output: ["banana"]
```

### 提示
> * 0 <= A.length <= 200
> * 0 <= B.length <= 200
> * 字符串A、B都仅包含空格个小写字母

### 解题思路
这个题目比较简单，可以考虑用字典来做。

### 解题代码
```python
import collections

class Solution:
    def uncommonFromSentences(self, A, B):
        """
        :type A: str
        :type B: str
        :rtype: List[str]
        """
        ans = []
        setA = set(A.split())
        setB = set(B.split())
        dictA = collections.Counter(A.split())
        dictB = collections.Counter(B.split())
        for k, v in dictA.items():
            if v == 1 and k not in setB:
                ans.append(k)
        for k, v in dictB.items():
            if v == 1 and k not in setA:
                ans.append(k)
        return ans
```

