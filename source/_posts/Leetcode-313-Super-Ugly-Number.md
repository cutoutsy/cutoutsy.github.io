---
layout: post
title: 'Leetcode 313: Super Ugly Number'
categories: 算法
date: 2018-08-22 10:14:13
tags:
    - Leetcode
    - 313
---
**难度：Medium**
相似题目：
* [264. Ugly Number II](https://leetcode.com/problems/ugly-number-ii/description/)

### 题目描述
编写一段程序来查找第n个超级丑数。
超级丑数是指其所有质因数都是长度为k的质数列表primes中的正整数。
<!--more-->
### 示例
```shell
输入: n = 12, primes = [2,7,13,19]
输出: 32 
解释: 给定长度为4的质数列表primes = [2,7,13,19]，前12个超级丑数序列为：[1,2,4,7,8,13,14,16,19,26,28,32]。
```
### 说明
* 1是任何给定primes的超级丑数。
* 给定primes中的数字以升序排列。
* 0 < k ≤ 100, 0 < n ≤ 106, 0 < primes[i] < 1000 
* 第n个超级丑数确保在32位有符整数范围内

### 解题思路
这个解题思路是我独自想出来的，后面看讨论区，和其中一个中文解释是差不多的。

主要思路是来模拟这个超级丑数序列的生成过程。首先我们使用ugly数组来保存丑数序列，长度为n，易知ugly[0] = 1, 且结果返回ugly[n-1]即可。

其实生成方式很简单，就是用primes数组中的素数去乘以它之前还未乘过的下标。这需要定义一个primes数组中每个素数该乘以ugly数组中的值的下标。定义这个数组为index，长度和primes数组是一样的，初始化为0。

比如我们现在来求ugly[1]，
```shell
primes[0] * ugly[index[0]] = 2
primes[1] * ugly[index[1]] = 7
primes[2] * ugly[index[2]] = 13
primes[3] * ugly[index[3]] = 19
```
我们可以看到最小值是2，则ugly[1] = 2，同时index[0]++，因为后面素数2要乘以ugly下标为1的数，下标为0的数已经计算过了。比如再求一个ugly[2],
```shell
primes[0] * ugly[index[0]] = 4
primes[1] * ugly[index[1]] = 7
primes[2] * ugly[index[2]] = 13
primes[3] * ugly[index[3]] = 19
```
最小值是4，所以ugly[2] = 4, 同时index[0]++。
按照上面的计算过程，可能存在和前一个值相等的情况，此时我们需要把对应下标加到大于前一个数之后，再进行计算。

**这个解法也是可以解决相似题目的。**

也可以看下讨论区相似的题解[Java solution，21ms，详细的中文解释](https://leetcode.com/problems/super-ugly-number/discuss/76292/Java-solution21ms)

### 解题代码
```Java
class Solution {
    public int nthSuperUglyNumber(int n, int[] primes) {
        int[] index = new int[primes.length];
        int[] ugly = new int[n];
        ugly[0] = 1;
        for (int i = 1; i < n; i++) {
            int min = Integer.MAX_VALUE;
            int tempIndex = -1;
            for (int j = 0; j < primes.length; j++) {
                while ((primes[j] * ugly[index[j]]) <= ugly[i-1]) {
                    index[j]++;
                }
                if ((primes[j] * ugly[index[j]]) < min) {
                    min = primes[j] * ugly[index[j]];
                    tempIndex = j;
                }
            }
            index[tempIndex]++;
            ugly[i] = min;
        }
        return ugly[n-1];
    }
}
```
