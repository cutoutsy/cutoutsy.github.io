---
layout: post
title: 'Leetcode 933: Number of Recent Calls'
categories: 算法
date: 2018-11-16 09:46:47
tags:
    - Leetcode
    - python3
    - 933
---
**难度：Easy**
### 题目描述
写一个RecentCounter类来计算最近的请求。

它只有一个方法：ping(int t)，其中t代表以毫秒为单位的某个时间。

返回从3000毫秒前到现在的ping数。

任何处于[t - 3000, t]时间范围之内的ping都将会被计算在内，包括当前（指t时刻）的ping。

保证每次对ping的调用都使用比之前更大的t值

<!--more-->

### 示例
```shell
输入：inputs = ["RecentCounter","ping","ping","ping","ping"], inputs = [[],[1],[100],[3001],[3002]]
输出：[null,1,2,3,3]
```

### 提示
* 每个测试用例最多调用10000次ping。
* 每个测试用例会使用严格递增的t值来调用ping。
* 每次调用 ping 都有 1 <= t <= 10^9。

### 解题思路
这个题目可能题意不太好理解，但是题目是很简单的，使用一个队列即可。每次将新加入的值-3000，然后将队列前小于这个值的弹出，函数最后返回队列的长度即可。具体可参考下面的代码。

### 解题代码
**Python3**
```python
class RecentCounter:

    def __init__(self):
        self.p = collections.deque()        

    def ping(self, t):
        self.p.append(t)
        while self.p[0] < t - 3000:
            self.p.popleft()
        return len(self.p)

# Your RecentCounter object will be instantiated and called as such:
# obj = RecentCounter()
# param_1 = obj.ping(t)
```
