---
layout: post
title: 'Leetcode 654: Maximum Binary Tree'
categories: 算法
date: 2018-07-27 19:49:43
tags:
    - Leetcode
    - 算法
    - 最大二叉树
---
**难度: medium**
### 题目描述
给定一个不含重复元素的整数数组。一个以此数组构建的最大二叉树定义如下：

二叉树的根是数组中的最大元素。
1. 左子树是通过数组中最大值左边部分构造出的最大二叉树。
2. 右子树是通过数组中最大值右边部分构造出的最大二叉树。
3. 通过给定的数组构建最大二叉树，并且输出这个树的根节点。

<!-- more -->

### 示例
```shell
输入: [3,2,1,6,0,5]
输入: 返回下面这棵树的根节点：

      6
    /   \
   3     5
    \    / 
     2  0   
       \
        1
```

### 注意
给定的数组大小在[1, 1000]之间

### 解题思路
这是一个关于二叉树的题目。一般二叉树的题目都是使用递归的方式，这个题目也不例外，思路比较简单，可以直接看下面的代码。

### 解题代码
```Java
class Solution {
    public TreeNode constructMaximumBinaryTree(int[] nums) {
        return maxNode(nums, 0, nums.length - 1);
        
    }
    
    public TreeNode maxNode(int[] nums, int start, int end) {
        if (start > end) {
            return null;
        }
        int max = Integer.MIN_VALUE;
        int maxIndex = -1;
        for (int i = start; i <= end; i++) {
            if (nums[i] > max) {
                max = nums[i];
                maxIndex = i;
            }
        }
        TreeNode node = new TreeNode(max);
        node.left = maxNode(nums, start, maxIndex - 1);
        node.right = maxNode(nums, maxIndex + 1, end);
        return node;
    }
}
```