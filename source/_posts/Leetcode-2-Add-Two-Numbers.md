---
layout: post
title: 'Leetcode 2: Add Two Numbers'
categories: 算法
date: 2018-09-25 10:27:24
tags:
    - Leetcode
    - 算法
    - Linked List
---
**难度：Medium**
### 题目描述
给定两个非空链表来表示两个非负整数。位数按照逆序方式存储，它们的每个节点只存储单个数字。将两数相加返回一个新的链表。

你可以假设除了数字 0 之外，这两个数字都不会以零开头。

<!--more-->

### 示例
```shell
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```

### 解题思路
这道题考察的是链表的操作，平常我们加两个数都是从后往前加，而这个反过来即可，进位是往后面进位。

### 解题代码
```Java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        List<Integer> res = new ArrayList<>();
        int p = 0;
        while (l1 != null && l2 != null) {
            int temp = l1.val + l2.val + p;
            res.add(temp % 10);
            p = temp / 10;
            l1 = l1.next;
            l2 = l2.next;
        }
        while (l1 != null) {
            int temp = l1.val + p;
            res.add(temp % 10);
            p = temp / 10;
            l1 = l1.next;
        }
        while (l2 != null) {
            int temp = l2.val + p;
            res.add(temp % 10);
            p = temp / 10;
            l2 = l2.next;
        }
        if (p != 0) {
            res.add(p);
        }
        ListNode head = new ListNode(res.get(0));
        ListNode ph = head;
        for (int i = 1; i < res.size(); i++) {
            ph.next = new ListNode(res.get(i));
            ph = ph.next;
        }
        return head;
    }
}
```
