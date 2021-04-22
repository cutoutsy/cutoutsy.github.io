---
layout: post
title: 'Leetcode 373: Find K Pairs with Smallest Sums'
categories: 算法
date: 2018-09-01 11:10:48
tags:
    - Leetcode
    - 373
---
**难度：Medium**
### 题目描述
给定两个以升序排列的整型数组nums1和nums2, 以及一个整数k。
定义一对值 (u,v)，其中第一个元素来自nums1，第二个元素来自nums2。
找到和最小的k对数字 (u1,v1), (u2,v2) ... (uk,vk)。

<!--more-->

### 示例
**示例1**
```shell
输入: nums1 = [1,7,11], nums2 = [2,4,6], k = 3
输出: [1,2],[1,4],[1,6]
解释: 返回序列中的前 3 对数：
     [1,2],[1,4],[1,6],[7,2],[7,4],[11,2],[7,6],[11,4],[11,6]
```
**示例2**
```shell
输入: nums1 = [1,1,2], nums2 = [1,2,3], k = 2
输出: [1,1],[1,1]
解释: 返回序列中的前 2 对数：
     [1,1],[1,1],[1,2],[2,1],[1,2],[2,2],[1,3],[1,3],[2,3]
```
**示例3**
```shell
输入: nums1 = [1,2], nums2 = [3], k = 3 
输出: [1,3],[2,3]
解释: 也可能序列中所有的数对都被返回:[1,3],[2,3]
```

### 解题思路
从题意上是说找到最小的k对数字，这让我们很容易就想到使用小根堆来做这个题。

我们可以把一对数字放到一个类中，然后实现比较器，将这个类放到小根堆中，再依次弹出前k个值就可以了。

### 解题代码
```Java
class Solution {

class Pair {
    int n1;
    int n2;
    public Pair(int n1, int n2) {
        this.n1 = n1;
        this.n2 = n2;
    }
}
    public List<int[]> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        PriorityQueue<Pair> pairs = new PriorityQueue<>(new Comparator<Pair>(){
            @Override
            public int compare(Pair p1, Pair p2) {
                return p1.n1 + p1.n2 - p2.n1 - p2.n2;
            }
            
        });
        List<int[]> ans = new ArrayList<>();
        int count = 0;
        for (int n1 : nums1) {
            for (int n2 : nums2) {
                pairs.add(new Pair(n1, n2));
            }
        }
        while (count < k && !pairs.isEmpty()) {
            Pair p = pairs.poll();
            int[] temp = new int[] {p.n1, p.n2};
            ans.add(temp);
            count++;
        }
        return ans;
    } 
}
```
