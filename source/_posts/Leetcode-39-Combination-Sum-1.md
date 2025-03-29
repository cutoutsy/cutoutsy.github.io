---
layout: post
title: 'Leetcode 39: Combination Sum'
categories: 算法
date: 2018-09-05 09:14:45
tags:
    - Leetcode
    - backtracking
---
**难度：medium**
### 题目描述
给定一个无重复元素的数组candidates和一个目标数target ，找出candidates中所有可以使数字和为target的组合。
candidates中的数字可以无限制重复被选取。

**说明：**
* 所有数字（包括 target）都是正整数。
* 解集不能包含重复的组合。
<!--more-->

### 示例

**示例1**
```shell
输入: candidates = [2,3,6,7], target = 7,
所求解集为:
[
  [7],
  [2,2,3]
]
```
**示例2**
```shell
输入: candidates = [2,3,5], target = 8,
所求解集为:
[
  [2,2,2,2],
  [2,3,3],
  [3,5]
]
```

### 解题思路
这道题的思路还是比较清晰的，就是用深度优先遍历+回溯，但是由于解集不能包含重复的组合，即每次遍历应该从当前位置开始，不包含之前的元素。这样才能保证不重复。

### 解题代码
```Java
class Solution {
    List<List<Integer>> ans = new ArrayList<>();
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        for (int i = 0; i < candidates.length; i++) {
            List<Integer> temp = new ArrayList<>();
            temp.add(candidates[i]);
            dfs(candidates, target - candidates[i], temp, i);
        }
        return ans;
    }
    
    public void dfs(int[] candidates, int target, List<Integer> list, int start) {
        if (target == 0) {
            ans.add(list);
            return;
        }
        if (target < 0) {
            return;
        }
        for (int i = start; i < candidates.length; i++) {
            List<Integer> temp = new ArrayList<>();
            for (int k : list) {
                temp.add(k);
            }
            temp.add(candidates[i]);
            dfs(candidates, target - candidates[i], temp, i);
        }
    }
}
```
