---
layout: post
title: 'Leetcode 515: Find Largest Value in Each Tree Row'
categories: 算法
date: 2018-08-06 09:31:53
tags:
    - Leetcode
    - 算法
    - 515
---
**难度：medium**
### 题目描述
您需要在二叉树的每一行中找到最大的值。

<!--more-->

### 示例
```shell
输入: 
          1
         / \
        3   2
       / \   \  
      5   3   9 

输出: [1, 3, 9]
```

### 解题思路
题目描述比较简单，也很容易理解，可以用BFS来做。我这里是使用类似二叉树按行遍历的做法，二叉树按行遍历可以掌握下，很多题目可以在这个基础上求解。

### 解题代码
```Java
class Solution {
    public List<Integer> largestValues(TreeNode root) {
        Queue<TreeNode> queue = new LinkedList<>();
         List<Integer> ans = new LinkedList<>();
         if (root == null) {
            return ans;
        }
        queue.offer(root);
        TreeNode last = root;
        TreeNode nLast = null;
        int max = Integer.MIN_VALUE;
        while (!queue.isEmpty()) {
            root = queue.poll();
            max = root.val > max ? root.val : max;
            if (root.left != null) {
                queue.offer(root.left);
                nLast = root.left;
            }
            if (root.right != null) {
                queue.offer(root.right);
                nLast = root.right;
            }
            if (root == last && !queue.isEmpty()) {
                ans.add(max);
                max = Integer.MIN_VALUE;
                last = nLast;
            }
        }
        ans.add(max);
        return ans;
    }
}
```
