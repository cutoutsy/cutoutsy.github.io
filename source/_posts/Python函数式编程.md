---
layout: post
title: Python函数式编程
date: 2020-08-09 18:15:07
categories: 编程	
tags:
	- Python
	- 函数式
	- 编程
---

### 函数式编程简介
函数式编程在最近几年比较热门。

### 高阶函数
函数名其实是指向函数的变量，比如以python内置函数abs为例，我们可以通过下面的代码来证明：
```python
>>> f = abs
>>> f(-4)
4
>>> abs = len
>>> abs(-4)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: object of type 'int' has no len()
>> abs([1, 2, 3])
3
```
变量可以指向函数，函数的参数可以接受变量，一个函数可以接受另一个函数作为参数。
我们把能接受函数做参数的函数称为**高阶函数**
<!-- more -->

**举个栗子**
```python
def add(x, y, f):
    ""
    f是一个函数
    ""
    return f(x) + f(y)

add(-5, 9, abs)   # 输出14
add(25, 9, math.sqrt)  # 输出8.0
```
### Python内置高阶函数
#### map()函数
**map()**函数是python内置的高阶函数，它接收一个**函数f**和一个**list**，并通过函数f依次作用再list的每个元素上，得到一个新的list并返回。

例如，对于list [1, 2, 3, 4, 5, 6, 7, 8, 9]

如果希望把list的每个元素都作平方，就可以用map()函数：

![map函数](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_python_function_map.jpg)

因此，我们只需要传入函数f(x)=x*x，就可以利用map()函数完成这个计算：
```python
def f(x):
    return x*x
print(map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
```
**输出结果：**
```python
[1, 4, 9, 10, 25, 36, 49, 64, 81]
```
**注意**
map()函数不改变原有的 list，而是返回一个新的 list。

由于list包含的元素可以是任何类型，因此，map() 不仅仅可以处理只包含数值的 list，事实上它可以处理包含任意类型的 list，只要传入的函数f可以处理这种数据类型。
#### reduce()函数
**reduce()**函数也是Python内置的一个高阶函数。reduce()函数接收的参数和 map()类似，**一个函数 f**，**一个list**，但行为和 map()不同，reduce()传入的函数 f 必须接收两个参数，reduce()对list的每个元素反复调用函数f，并返回最终结果值。

例如，编写一个f函数，接收x和y，返回x和y的和：
```python
def f(x, y):
    return x + y
```
调用**reduce(f, [1, 3, 5, 7, 9])**时，reduce函数将做如下计算：
```python
先计算头两个元素：f(1, 3)，结果为4；
再把结果和第3个元素计算：f(4, 5)，结果为9；
再把结果和第4个元素计算：f(9, 7)，结果为16；
再把结果和第5个元素计算：f(16, 9)，结果为25；
由于没有更多的元素了，计算结束，返回结果25。
```
reduce()还可以接收第3个可选参数，作为计算的初始值。如果把初始值设为100，计算：

reduce(f, [1, 3, 5, 7, 9], 100)
结果将变为125，因为第一轮计算是：

计算初始值和第一个元素：f(100, 1)，结果为101。

Python内置了求和函数sum()，但是没有内置求积的函数，我们可以用reduce来实现一下：
```python
def prod(x, y):
    return x*y

print reduce(prod, [2, 4, 5, 7, 12])  # 输出3360
```
#### filter()函数
**filter()**函数是 Python 内置的另一个有用的高阶函数，filter()函数接收一个**函数f**和一个**list**，这个函数 f 的作用是对每个元素进行判断，返回True或False，**filter()根据判断结果自动过滤掉不符合条件的元素，返回由符合条件元素组成的新list**。

例如，要从一个list [1, 4, 6, 7, 9, 12, 17]中删除偶数，保留奇数，首先，要编写一个判断奇数的函数：
```python
def is_odd(x):
    return x % 2 == 1
```
然后，利用filter()过滤掉偶数：
```python
filter(is_odd, [1, 4, 6, 7, 9, 12, 17])
```
**结果**：[1, 7, 9, 17]

利用filter()，可以完成很多有用的功能，例如，删除 None 或者空字符串：
```python
def is_not_empty(s):
    return s and len(s.strip()) > 0
filter(is_not_empty, ['test', None, '', 'str', '  ', 'END'])
```
**结果**：['test', 'str', 'END']

#### sorted()函数
这个函数我们应该很熟悉，**sorted()**也是一个高阶函数，它可以接收一个比较函数来实现自定义排序，比较函数的定义是，传入两个待比较的元素x, y，**如果x应该排在y的前面，返回-1，如果x应该排在y的后面，返回1。如果x和y相等，返回0**。

