---
layout: post
title: 'Leetcode 814: Binary Tree Pruning'
categories: 算法
date: 2018-07-31 10:10:47
tags:
    - Leetcode
    - 算法
    - 814
---
**难度：Medium**

### 题目描述
给定二叉树根结点 root ，此外树的每个结点的值要么是 0，要么是 1。

返回移除了所有不包含 1 的子树的原二叉树。

( 节点 X 的子树为 X 本身，以及所有 X 的后代。）

<!-- more -->

### 示例
#### 示例1
```shell
输入: [1,null,0,0,1]
输出: [1,null,0,null,1]
 
解释: 
只有红色节点满足条件“所有不包含 1 的子树”。
右图为返回的答案。
```
![example1](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/04/06/1028_2.png)

#### 示例2
```shell
输入: [1,0,1,0,0,0,1]
输出: [1,null,1,null,1]
```
![example2](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/04/06/1028_1.png)

### 示例3
```shell
输入: [1,1,0,1,1,0,1,0]
输出: [1,1,0,1,1,null,1]
```
![example3](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/04/05/1028.png)

### 说明

* 给定的二叉树最多有100个节点。
* 每个节点的值只会为0或1。

### 解题思路
对于二叉树的题目，我们首先考虑只有3个节点（即父节点，两个左右节点）的情况，然后再递归就好了。

### 解题代码
```Java
class Solution {
    public TreeNode pruneTree(TreeNode root) {
        hasOneNode(root);
        return root;
    }
    
    public boolean hasOneNode(TreeNode root) {
        if (root == null) {
            return false;
        }
        if (!hasOneNode(root.left)) {
            root.left = null;
        } 
        if (!hasOneNode(root.right)) {
            root.right = null;
        } 
        
        if (hasOneNode(root.left) || hasOneNode(root.right)) {
            return true;
        }
        
        if (root.val == 0) {
            return false;
        }
        return true;
    }
    
}
```