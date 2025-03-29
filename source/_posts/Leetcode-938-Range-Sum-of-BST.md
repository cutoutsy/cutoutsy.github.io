---
layout: post
title: 'Leetcode 938: Range Sum of BST'
categories: 算法
date: 2018-11-13 09:54:18
tags:
    - Leetcode
    - 938
    - Python3
---
**难度：Medium**
### 题目描述
给定一个搜索二叉树的根节点，返回在L到R值（包括L，R）之间的所有节点的和。

二叉搜索树保证节点值是唯一的。

### 示例
**示例1**
```shell
输入: root = [10,5,15,3,7,null,18], L = 7, R = 15
输出: 32
```
<!--more-->
**示例2**
```shell
输入: root = [10,5,15,3,7,13,18,1,null,6], L = 6, R = 10
输出: 23
```

### 注意
* 搜索二叉树的节点最多10000
* 最后的结果保证少于$2^{31}$

### 解题思路
对于二叉树的题目，一般还是从递归入手。可以让当前节点的值和L、R比较，根据情况再递归的进入子节点。

### 解题代码
**Python3**
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def rangeSumBST(self, root, L, R):
        """
        :type root: TreeNode
        :type L: int
        :type R: int
        :rtype: int
        """
        def loop(root, L, R):
            ans = 0
            if root:
                if root.val > R:
                    ans += loop(root.left, L, R)
                elif root.val < L:
                    ans += loop(root.right, L, R)
                else:
                    ans += root.val
                    ans += loop(root.left, L, R)
                    ans += loop(root.right, L, R)
            return ans
        return loop(root, L, R)
```