---
layout: post
title: 'Leetcode 508: Most Frequent Subtree Sum'
categories: 算法
date: 2018-08-11 09:23:06
tags:
    - Leetcode
    - Tree
    - HashTable
---
**难度：Medium**
### 题目描述
给出二叉树的根，找出出现次数最多的子树元素和。一个结点的子树元素和定义为以该结点为根的二叉树上所有结点的元素之和（包括结点本身）。然后求出出现次数最多的子树元素和。如果有多个元素出现的次数相同，返回所有出现次数最多的元素（不限顺序）。
<!--more-->

### 示例
#### 示例1
```shell
  5
 /  \
2   -3
```
返回 [2, -3, 4]，所有的值均只出现一次，以任意顺序返回所有值。
#### 示例2
```shell
  5
 /  \
2   -5
```
返回[2]，只有2出现两次，-5只出现1次。

### 注意
假设任意子树元素和均可以用32位有符号整数表示。

### 解题思路
关于二叉树的题目，首先想到的是递归，这个题目也不例外，我们可以递归的求子树元素和，并使用Hash Table进行统计。

### 解题代码
```Java
class Solution {
    public int[] findFrequentTreeSum(TreeNode root) {
        if (root == null)   return new int[] {};
        HashMap<Integer, Integer> times = new HashMap<>();
        treeSum(root, times);
        int max = Collections.max(times.values());
        List<Integer> ans = new ArrayList<>();
        for (Integer key : times.keySet()) {
            if (times.get(key) == max) {
                ans.add(key);
            }
        }
        int[] re = new int[ans.size()];
        for (int i = 0; i < ans.size(); i++) {
            re[i] = ans.get(i);
        }
        return re;
    }
    
    public int treeSum(TreeNode root, HashMap<Integer, Integer> times) {
        if (root == null) {
            return 0;
        }
        int left = treeSum(root.left, times);
        int right = treeSum(root.right, times);
        int sum = root.val + left + right;
        times.put(sum, times.getOrDefault(sum, 0) + 1);
        return sum;
    }
}
```