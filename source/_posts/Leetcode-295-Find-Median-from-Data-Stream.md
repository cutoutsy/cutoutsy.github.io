---
layout: post
title: 'Leetcode 295: Find Median from Data Stream'
categories: 算法
date: 2018-08-14 10:41:37
tags:
    - Leetcode
    - 295
    - 堆
---
**难度：Hard**
### 题目描述
中位数是有序列表中间的数。如果列表长度是偶数，中位数则是中间两个数的平均值。
例如，
[2,3,4]的中位数是3

[2,3]的中位数是(2 + 3) / 2 = 2.5

设计一个支持以下两种操作的数据结构：
* void addNum(int num) - 从数据流中添加一个整数到数据结构中。
* double findMedian() - 返回目前所有元素的中位数。
<!-- more -->

### 示例
```shell
addNum(1)
addNum(2)
findMedian() -> 1.5
addNum(3) 
findMedian() -> 2
```

### 解题思路
这个题目在Leetcode上难度是困难，之前在看面经的时候有看到问这个题目，所以找出来做一做。
解法主要是利用两个堆结构，一个是大根堆，保存数据流中较小的一半，堆顶为较大值。另一个为小根堆，保存数据流中较大的一半，堆定为较小值。
新加入的数根据与两个堆的堆顶的大小关系，选择放进大根堆或者小根堆。
当任何一个堆的size比另一个的size大2时，进行调整，保证两个堆size最多相差1。
下面的代码是用Java写的，Java的优先队列PriorityQueue就是堆，默认是小根堆，我们可以通过实现Comparator接口，来实现大根堆。

### 解题代码
```Java
class MedianFinder {
    
    private PriorityQueue<Integer> minHeap;
    private PriorityQueue<Integer> maxHeap;
    
    public MedianFinder() {
        Comparator<Integer> deComparator = new decreaseComparator();
        this.minHeap = new PriorityQueue<>();
        this.maxHeap = new PriorityQueue<>(deComparator);
    }
    
    public void addNum(int num) {
        if (this.maxHeap.isEmpty()) {
            this.maxHeap.add(num);
            return;
        }
        if (this.maxHeap.peek() >= num) {
            this.maxHeap.add(num);
        } else {
            if (this.minHeap.isEmpty()) {
                this.minHeap.add(num);
                return;
            }
            if (this.minHeap.peek() > num) {
                this.maxHeap.add(num);
            } else {
                this.minHeap.add(num);
            }
        }
        if (this.maxHeap.size() == this.minHeap.size() + 2) {
            this.minHeap.add(this.maxHeap.poll());
        }
        if (this.minHeap.size() == this.maxHeap.size() + 2) {
            this.maxHeap.add(this.minHeap.poll());
        }
    }
    
    class decreaseComparator implements Comparator<Integer>{
        @Override
        public int compare(Integer x, Integer y)
        {
            if (x < y)  return 1;
            if (x > y)  return -1;
            return 0;
        }
    }
    
    public double findMedian() {
        long maxSize = this.maxHeap.size();
        long minSize = this.minHeap.size();
        Integer maxHead = this.maxHeap.peek();
        Integer minHead = this.minHeap.peek();
        if (((maxSize + minSize) & 1) == 0) {
            return (maxHead + minHead) / 2.0;
        } else if (maxSize > minSize) {
            return maxHead;
        } else {
            return minHead;
        }
    }
}
```