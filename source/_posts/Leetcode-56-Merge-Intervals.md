---
layout: post
title: 'Leetcode 56: Merge Intervals'
categories: 算法
date: 2018-08-24 10:44:23
tags:
    - leetcode
    - 56
    - Array
---
**难度：Medium**
### 题目描述
给出一个区间的集合，请合并所有重叠的区间。

### 示例
**示例1**
```shell
输入: [[1,3],[2,6],[8,10],[15,18]]
输出: [[1,6],[8,10],[15,18]]
解释: 区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
```
<!--more-->

**示例2**
```shell
输入: [[1,4],[4,5]]
输出: [[1,5]]
解释: 区间 [1,4] 和 [4,5] 可被视为重叠区间。
```

### 解题思路
昨天在做keep笔试题的时候有一道编程题和这很相似，后面听同学说了下，然后来把这道题做了。

这个题思路还是比较简单的，主要是要对区间进行排序，如果是Java语言的话，就是主要考察了比较器的操作。将区间排序后，然后再进行合并就可以了。合并的时候需要注意两点：
* 可能存在多个区间进行合并，并不止示例中的两个区间
* 可能前一个区间的end大于后一个区间的end，所以合并时需要比较两个区间end的大小。

### 解题代码
```Java
/**
 * Definition for an interval.
 * public class Interval {
 *     int start;
 *     int end;
 *     Interval() { start = 0; end = 0; }
 *     Interval(int s, int e) { start = s; end = e; }
 * }
 */
class Solution {
    public List<Interval> merge(List<Interval> intervals) {
        List<Interval> ans = new LinkedList<>();
        if (intervals == null || intervals.size() == 0) {
            return ans;
        }
        Collections.sort(intervals, new Comparator<Interval>(){
            @Override
            public int compare(Interval inte1, Interval inte2) {
                if (inte1.start == inte2.start) return inte1.end - inte2.end;
                return inte1.start - inte2.start;
            }
        });
        ans.add(intervals.get(0));
        for (int i = 1; i < intervals.size(); i++) {
            Interval last = ans.get(ans.size() - 1);
            if (last.end >= intervals.get(i).start) {
                ans.remove(last);
                ans.add(new Interval(last.start, Math.max(last.end,intervals.get(i).end)));
            } else {
                ans.add(intervals.get(i));
            }
        }
        return ans;
    }
}

```