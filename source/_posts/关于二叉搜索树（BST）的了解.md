---
layout: post
title: 关于二叉搜索树（BST）的了解
date: 2017-09-10 21:01:14
categories: 算法	
tags:
	- 二叉搜索树
	- BST
---

### 写在前面
最近在leetcode上做了一些关于二叉搜索树（BST）的题目，仔细看了下关于BST的资料，这儿自己做一个简单的总结，可能在后面的题目中也会遇到关于BST更难的题（我是按顺序简单到困难），也方便查阅。

### 简单介绍
树是一种重要的数据结构，在面试中也问得比较多。

二叉搜索树首先是二叉树。二叉树是每个节点最多只有两个分支的树结构，通常称作“左子树”和“右子树”，二叉树的分支具有左右次序，不能颠倒。关于二叉树有一些性质以及存在其他的二叉树，在这我不做过多介绍。
<!-- more -->
二叉搜索树又称有序二叉树、排序二叉树。因为它的节点是具有一定顺序的。
我们来看下它的主要性质，通过这些主要性质你就可以了解到二叉搜索树是一种什么样的二叉树。
性质：
1. 若任意节点的左子树不空，则左子树上所有节点的值均小于它的根节点的值；
2. 若任意节点的右子树不空，则右子树上所有节点的值均大于它的根节点的值；
3. 任意节点的左、右子树也分别为二叉查找树；
4. 没有键值相等的节点。

### 基本操作
#### 1. 定义
```Python
class TreeNode(object):
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None
```
#### 2. 查找
流程：
a.从root节点开始
b.若root值等于查找值，返回真
c.若root值小于查找值，找右节点
d.若root值大于查找值，找左节点
e.最后都没找到，返回假
代码：
```Python
def find(self, root, value):
    """
    :param root: TreeNode
    :param value: int
    :return: boolean
    """
    while root:
        if root.val == value:
            return True
        elif root.val > value:
            root = root.left
        else:
            root = root.right
    return False
```

#### 3. 插入（构造BST）
流程：
a. 从root节点开始
b. 若root为空，则root为插入值
c. 循环：
d. 若当前节点值大于插入值，找左节点
e. 若当前节点值小于插入值，找右节点
代码：
```Python
def inset(self, root, num):
    """
    :param num: int
    :param root: TreeNode
    :return: TreeNode
    """
    newNode = TreeNode(num)
    ans = root
    if not root:
        return newNode
    while True:
        parent = root
        if num < root.val:
            root = root.left
            if not root:
                parent.left = newNode
                return ans
        else:
            root = root.right
            if not root:
                parent.right = newNode
                return ans
```
#### 4. 删除
根据待删除节点分三种情况：
1） 没有子节点
直接删除该节点，然后父节点指为空
2）只有一个子节点
直接删除该节点，然后父节点指向子节点
3）有两个子节点
对于有两个节点的有两种方式，用被删除节点的左子树的最右节点或者右子树的最左节点替代删除节点，并修改相应的最左最右的父节点的指针。
```python
def deleteNode(self, root, key):
    if not root:
        print 'not find key:', key
    elif key < root.key:
        root.left = self.deleteNode(root.left, key)
    elif key > root.key:
        root.right = self.deleteNode(root.right, key)
    elif root.left and root.right:
        right_min = self.find_min(self.root.right)
        root.key = right_min.key
        root.right = self.deleteNode(root.right, right_min.key)
    elif root.left:
        root = root.left
    elif root.right:
        root = root.right
    else；
        root = None
    return root

def find_min(self, root):
    if not root.left:
        return root
    return self.find_min(root.left)
```