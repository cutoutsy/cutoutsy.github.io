---
layout: post
title: 'Leetcode 382: Linked List Random Node'
categories: 算法
date: 2018-07-26 11:22:53
tags:
    - Leetcode
    - 算法
    - 水塘抽样
---
### 题目描述
给定一个单链表，随机选择链表的一个节点，并返回相应的节点值。保证每个节点被选的概率一样。

**注意**
链表长度可能非常大，使用很多额外空间的解决方案将不会通过。

<!-- more -->

### 示例
```shell
// 初始化一个单链表 [1,2,3].
ListNode head = new ListNode(1);
head.next = new ListNode(2);
head.next.next = new ListNode(3);
Solution solution = new Solution(head);

// getRandom()方法应随机返回1,2,3中的一个，保证每个元素被返回的概率相等。
solution.getRandom();
```
### 解题思路
这是一个经典的水池抽样问题，给定一个数据流，这个数据流的长度很大或者未知，并且对改数据流中的数据只能访问一次。请写出一个随机选择算法，使得数据流中所有数据被选中的概率相等。

我们可以采用数学归纳法进行分析：
1) 长度为1，只有一个数据，直接返回即可
2) 长度为2，在读取第一个数据，并不是最后一个数据，不能直接返回，存储起来。到第二个数据时，发现已经结束，那么就是0.5的概率返回其中一个，这是我们可以生成一个[0, 1)的随机数p，如果p<0.5，则返回第二个，反之返回保存的第一个的值。
3) 长度为3，在长度为2时，我们已经以$\frac{1}{2}$的概率保存了前两个中的一个值，现在是要以$\frac{1}{3}$概率返回数据，那么此时可以以$\frac{1}{3}$的概率保存当前值，即生成的$p < \frac{1}{3}$时，保存当前值，那么保存前面的一个值的概率则为：$\frac{1}{2} * (1 - \frac{1}{3}) = \frac{1}{3}$。

依次类推，在去第n个数据时，生成一个[0, 1)的随机数p，如果p小于$\frac{1}{n}$，则保留第n个数，反之则继续保留前面的数。直到数据流结束，返回此数。

### 解题代码
```Java
class Solution {

    /** @param head The linked list's head.
        Note that the head is guaranteed to be not null, so it contains at least one node. */
    ListNode head = null;
    public Solution(ListNode head) {
        this.head = head;
    }
    
    /** Returns a random node's value. */
    public int getRandom() {
        ListNode temp = head;
        int idx = 1;
        int ans = 0;
        Random random = new Random();
        while (temp != null) {
            if (idx == 1) {
                ans = temp.val;
            } else {
                if (random.nextDouble() < (1.0 / idx)) {
                    ans = temp.val;
                }
            }
            temp = temp.next;
            idx++;
        }
        return ans;
    }
}

```

### 推广
如果要求返回的结果长度为k，我们只需把上面的$\frac{1}{n}$变为$\frac{k}{n}$即可。

在取前面k个数，直接保留，对于第n个数，生成一个[0, 1)的随机数，如果小于$\frac{k}{n}$，随机选取前面k个中一个进行替换，大于$\frac{k}{n}$，继续保留前面的数，直到数据流结束。