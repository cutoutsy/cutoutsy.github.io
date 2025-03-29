---
layout: post
title: 'Leetcode 846: Hand of Straights'
categories: 算法
date: 2018-07-25 09:43:03
tags:
    - Leetcode
    - 846
    - 算法
---
### 题目描述
爱丽丝有一手（hand）由整数数组给定的牌。 

现在她想把牌重新排列成组，使得每个组的大小都是W，且由W张连续的牌组成。

如果她可以完成分组就返回true，否则返回false。
<!--more-->
### 示例
#### 示例1
```shell
输入：hand = [1,2,3,6,2,3,4,7,8], W = 3
输出：true
解释：爱丽丝的手牌可以被重新排列为 [1,2,3]，[2,3,4]，[6,7,8]。
```
#### 示例2
```shell
输入：hand = [1,2,3,4,5], W = 4
输出：false
解释：爱丽丝的手牌无法被重新排列成几个大小为 4 的组。
```

### 提示
* 1 <= hand.length <= 10000
* 0 <= hand[i] <= 10^9
* 1 <= W <= hand.length

### 解题思路
按照题目描述，可以编写程序来模拟这个过程。

### 解题代码
```Java
public boolean isNStraightHand(int[] hand, int W) {
    int n = hand.length;
    if (n % W != 0) {
        return false;
    }
    Arrays.sort(hand);
    List<Integer> nums = new ArrayList<>();
    for (int ele : hand) {
        nums.add(ele);
    }
    for (int i = 0; i < (n / W); i++) {
        Integer start = nums.get(0);    //start is object
        for (int j = 0; j < W; j++) {
            if (nums.remove(start++)){
                continue;
            } else {
                return false;
            }
        }
    }
    return true;
}
```
