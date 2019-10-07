---
layout: post
title: 'Leetcode 856: Score of Parenthese'
categories: 算法
date: 2018-08-10 09:37:54
tags:
    - 算法
    - leetcode
    - Stack
---
### 题目描述
给定一个平衡括号字符串 S，按下述规则计算该字符串的分数：
* () 得 1 分。
* AB 得 A + B 分，其中 A 和 B 是平衡括号字符串。
* (A) 得 2 * A 分，其中 A 是平衡括号字符串。
<!-- more -->

### 示例
#### 示例1
```shell
输入： "()"
输出： 1
```
#### 示例2
```shell
输入： "(())"
输出： 2
```
#### 示例3
```shell
输入： "()()"
输出： 2
```
#### 示例4
```shell
输入： "(()(()))"
输出： 6
```

### 提示
* S是平衡括号字符串，且只含有 ( 和 ) 。
* 2 <= S.length <= 50

### 解题思路
这道题我是看了讨论区别人的解法后做出来的。
开始我思路也是用栈，不过我想的是用两个栈，不过没有思考出一个具体的思路。下面代码这个思路比较巧妙和简单，值得借鉴。

### 解题代码
```Java
class Solution {
    public int scoreOfParentheses(String S) {
        Stack<Integer> stack = new Stack<>();
        for (char c : S.toCharArray()) {
            if (c == '(') {
                stack.push(-1);
            } else {
                int cur = 0;
                while (stack.peek() != -1) {
                    cur += stack.pop();
                }
                stack.pop();
                stack.push(cur == 0 ? 1 : cur * 2);
            }
        }
        int sum = 0;
        while (!stack.isEmpty()) {
            sum += stack.pop();
        }
        return sum;
    }
}
```