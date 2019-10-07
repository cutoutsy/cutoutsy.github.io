---
layout: post
title: 'Leetcode 377: Combination Sum IV'
categories: 算法
date: 2018-08-13 09:53:01
tags:
    - Leetcode
    - 377
    - 动态规划
---
### 题目描述
给定一个由正整数组成且不存在重复数字的数组，找出和为给定目标正整数的组合的个数。
<!-- more -->
### 示例
```shell
nums = [1, 2, 3]
target = 4

所有可能的组合为：
(1, 1, 1, 1)
(1, 1, 2)
(1, 2, 1)
(1, 3)
(2, 1, 1)
(2, 2)
(3, 1)

请注意，顺序不同的序列被视作不同的组合。

因此输出为 7。
```

### 解题思路
这是一个比较典型的动态规划问题。假设数组中元素为3个：m, n, k。那么我们可以知道任何一个数num，即dp[num] = dp[num - m] + dp[num - n] + dp[num - k]。
比如示例中，dp[4] = dp[3] + dp[2] + dp[1]，而dp[3] = dp[2] + dp[1] + dp[0]，这儿的dp[0] = 1。所以我们从dp[0]一次往后计算，即可得到dp[target]。

### 解题代码
```Java
class Solution {
    public int combinationSum4(int[] nums, int target) {
        int[] dp = new int[target+1];
        dp[0] = 1;
        for (int i = 1; i < dp.length; i++) {
            for (int ele : nums) {
                if (i - ele >= 0) dp[i] += dp[i-ele];
            }
        }
        return dp[target];
    }
}
```