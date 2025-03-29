---
layout: post
title: 2017校招-今日头条：String Shifting
categories: 算法
date: 2018-07-08 11:23:52
tags:
    - 2017校招
    - 算法
    - 今日头条
---
### 题目描述
我们规定对一个字符串的shift操作如下：
shift(“ABCD”, 0) = “ABCD”
shift(“ABCD”, 1) = “BCDA”
shift(“ABCD”, 2) = “CDAB”
换言之, 我们把最左侧的N个字符剪切下来, 按序附加到了右侧。
给定一个长度为n的字符串，我们规定最多可以进行n次向左的循环shift操作。如果shift(string, x) = string (0＜= x ＜n), 我们称其为一次匹配(match)。求在shift过程中出现匹配的次数。

<!-- more -->

### 输入描述
> 输入仅包括一个给定的字符串，只包含大小写字符。

### 输出描述
> 输出仅包括一行，即所求的匹配次数。

### 示例
> 输入
    > byebyebye
> 输出
    > 3

### 解题思路
首先这个题目可以按照题目要求，模拟shift操作，进行字符串翻转，然后进行比较，这样的时间复杂度太高，不能通过所有的测试用例。

其实换个角度来思考，这个题目是要求我们查找循环相等的字符串，比如示例中的“byebyebye”，那么循环相等的为“bye”，然后再用字符串的长度除以循环字符串的长度即为答案。

关于寻找循环字符串，涉及到使用kmp算法中的next数组。

### 解题代码
```Java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        String input = sc.nextLine();
        int n = input.length();
        int k = getNextArray(input);
        System.out.println(n % (n-k) == 0 ? n / (n-k) : 1);
    }

    private static int getNextArray(String str) {
        char[] charStr = str.toCharArray();
        if (charStr.length == 1) {
            return 0;
        }
        int[] next = new int[charStr.length + 1];
        next[0] = -1;
        next[1] = 0;
        int pos = 2;
        int cn = 0;
        while (pos < charStr.length + 1) {
            if (charStr[pos - 1] == charStr[cn]) {
                next[pos++] = ++cn;
            }
            else if (cn > 0) {
                cn = next[cn];
            } else {
                next[pos++] = 0;
            }
        }
        return next[charStr.length];
        }
}
```
