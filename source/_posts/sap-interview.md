---
laypost: post
title: SAP面试经历
date: 2016-09-26 00:01:48
tags: 
	- SAP
	- 面试
---

一面：
1、vim有几种模式，vim替换操作
vim有三种模式（只答出两种）：命令模式(command mode)、插入模式(insert mode)、底线命令模式(last line mode)
替换：[addr]s/源字符串/目的字符串/[option]
2、线程有几种状态（4种）
运行(running)，休眠(Sleeping)，等待(Wait)，监视(Monitor)
3、查找一个目录下所有文件里面的某个关键字，同时显示行号
grep -rn "keyword" *
<!-- more -->
4、数据库dml和ddl是什么
DML（data manipulation language）:它们是SELECT、UPDATE、INSERT、DELETE，就象它的名字一样，这4条命令是用来对数据库里的数据进行操作的语言
DDL（data definition language）：DDL比DML要多，主要的命令有CREATE、ALTER、DROP等，DDL主要是用在定义或改变表（TABLE）的结构，数据类型，表之间的链接和约束等初始化工作上，他们大多在建立表时使用
5、抽象类和接口的区别
抽象类是用来捕捉子类的通用特性的。它不能被实例化，只能被用作子类的超类。抽象类是被用来创建继承层级里子类的模板。
接口是抽象方法的集合。如果一个类实现了某个接口，那么它就继承了这个接口的抽象方法。这就像契约模式，如果实现了这个接口，那么就必须确保使用这些方法。接口只是一种形式，接口自身不能做任何事情。
6、判断一个对象是否死亡，是否要进行回收
最近在看《深入理解Java虚拟机》这本书，看过之后没有怎么记住，就答了个对象没有在被其他对象引用就可以被回收了，根据书上所讲，首先判断对象是否“死去”有两种算法：引用计数算法、可达性分析算法；简单来说引用计数算法就是每当有一个地方引用它时，计数器值就加1，当引用失效时，计数器值就减1，任何时刻计数器为0的对象是不可能再被使用的（这也是好多人在面试时给予的答案）。但是Java虚拟机里面没有选用这种方法来管理内存，主要原因是很难解决对象之间互循环引用的问题。Java虚拟机采用第二种算法来管理内存，可达性分析算法是一个GC Roots和引用链(Reference Chain)，当一个对象到GC Roots没有任何引用链相连，则证明此对象是不可用的，将会被判定为是可回收的对象（正确答案），当然具体情况更复杂，即使在可达性分析算法中不可达的对象，也并非是“非死不可”的，要真正宣告一个对象的死亡，至少要经历两次标记过程，过程就不详细叙述了。
7、文件的755三个数字分别对应什么
7对应所有者对文件的操作权限
5对应所有者所在组的其他用户对这些文件的操作权限
5对应其他用户组对这些文件的操作权限

二面：
关于awk,sed,sort的命令的操作
这个主要就是linux文本操作的一些命令
html5和原来html的区别
1、文档声明
html:
```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
```
html5:
```html
<!DOCTYPE html>
```
可以看出，html5文档声明要比html简单很多
2、结构语义
html5提供了一些新的html5标签,比如
```html
<header> <nav> <article> <aside> <footer>
```
3、新功能
html5提供了强大的绘图功能，html5可以通过强大的绘图功能，加上JS可以实现。
Canvas标签 SVG标签
视频标签

Java类能不能多继承，怎么实现类似的功能
Java不能多继承，可以通过接口和内部类来实现类似的功能。

python列表解析
列表解析(List comprehension)来自于函数式编程Haskell
语法：[expr for iter_var in iterable]
```python
a = [x+1 for x in range(1, 11)]
print a
[2, 3, 4, 5, 6, 7, 8, 9, 10, 11]
```

shell调试（如何调试）
首先可以使用shell的执行选项，-x选项是常用的跟踪和调试shell脚本的手段
```shell
-n 只读取脚本，不实际执行，可用于测试shell脚本是否存在语法错误
-x 进入跟踪模式，显示所执行的每一条命令
-c "string" 从string中读取命令执行
```
还可以对"-x"选项进行增强
其他就是使用trap命令，tee命令，使用"调试钩子"等
