---
layout: post
title: 'Leetcode 841: Keys and Rooms'
categories: 算法
date: 2018-08-01 09:15:43
tags:
    - Leetcode
    - 841
    - DFS
---
**难度：Medium**
### 题目描述
有N个房间，开始时你位于0号房间。每个房间有不同的号码：0，1，2，...，N-1，并且房间里可能有一些钥匙能使你进入下一个房间。

在形式上，对于每个房间i都有一个钥匙列表rooms[i]，每个钥匙rooms[i][j]由[0,1，...，N-1]中的一个整数表示，其中N=rooms.length。 钥匙rooms[i][j]=v可以打开编号为v的房间。

<!-- more -->

最初，除0号房间外的其余所有房间都被锁住。

你可以自由地在房间之间来回走动。

如果能进入每个房间返回true，否则返回false。

### 示例
#### 示例1
```shell
输入: [[1],[2],[3],[]]
输出: true
解释:  
我们从0号房间开始，拿到钥匙1。
之后我们去1号房间，拿到钥匙2。
然后我们去2号房间，拿到钥匙3。
最后我们去了3号房间。
由于我们能够进入每个房间，我们返回true。
```
#### 示例2
```shell
输入：[[1,3],[3,0,1],[2],[0]]
输出：false
解释：我们不能进入2号房间。
```

### 提示
* 1 <= rooms.length <= 1000
* 0 <= rooms[i].length <= 1000
* 所有房间中的钥匙数量总计不超过3000。

### 解题思路
这个题目其实整体来看是一个图，房间就是图中的点，钥匙就是一个点到另一个点的有向边。最后就是找出哪个点没有访问到。这个我们很容易想到用深度优先遍历来做。
具体参见代码。

### 解题代码
```Java
class Solution {
    public boolean canVisitAllRooms(List<List<Integer>> rooms) {
        int n = rooms.size();
        boolean[] visited = new boolean[n];
        dfs(rooms, 0, visited);
        visited[0] = true;
        for (boolean ele : visited) {
            if (!ele) {
                return false;
            }
        }
        return true;
    }
    
    public void dfs(List<List<Integer>> rooms, int r, boolean[] visited) {
        for (Integer ele : rooms.get(r)) {
            if (!visited[ele]) {
                visited[ele] = true;
                dfs(rooms, ele, visited);
            }
        }
    }
}
```
