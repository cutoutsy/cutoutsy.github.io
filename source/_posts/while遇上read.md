---
layout: post
title: linux shell:while遇上read
date: 2016-11-29 08:23:53
tags:
	- linux
	- shell
---

前不久在公司修改一个脚本的时候，把两个值写入到一个文件中，然后在另一个shell读取这个文件然后赋值给两个变量，shell脚本大概是这样写的：
```shell
#!/bin/bash

count=1
cat ./test.data | while read line
do
        if [ $count -eq 1 ]
        then
                s1=$line
                echo $s1
        else
                s2=$line
                echo $s2
        fi
        count=$[ $count+1 ]
done
echo $s1
echo $s2
```
<!-- more -->
test.data里面是两行值，比如：
```shell
aaa
bbb
```
当我在后面的脚本中使用这两个值时，却发现没有值。
通过echo我们可以看到在while循环里面，\$s1和$s2是有值的，只是在循环外面没有值。

最初我认为是变量作用范围的问题，所以我们先来看看linux shell的变量作用域，shell脚本变量主要分为环境变量和用户变量以及反引号赋值的变量。shell维护着一组环境变量，用来记录特定的系统信息。比如系统的名称，登陆到系统上的用户的名称，用户的系统ID等。除了环境变量，shell脚本还允许在脚本中定义和使用自己的变量。定义变量允许临时存储数据并在整个脚本中使用。在脚本的整个生命周期里，shell脚本中定义的变量会一直保持它们的值，直到脚本完成时删除掉。也可以通过反引号把命令的输出赋值给变量。比如:
```shell
test=`date`
```
linux shell也是有函数的概念的，但是在函数里面定义的变量并不像C语言里面似的是局部变量，在里面同样能改变外部变量的值，里面的变量也能在外面输出。比如下面这个脚本：
```shell
#!/bin/sh

var1="hello"
myfunc() {
        var1="one two three"
        for x in $var1
        do
                echo $x > /dev/null
        done
}
myfunc
echo $var1 $x
```
输出：
```shell
one two three three
```
可以看到linux shell函数里面并不是局部变量，但是我们可以通过使用"local"命令来防止全局变量，而把它们放在局部名称空间中。比如下面这个脚本：
```shell
#!/bin/sh

var1="hello"
myfunc() {
        local x
        local var1="one two three"
        for x in $var1
        do
                echo $x > /dev/null
        done
}
myfunc
echo $var1 $x
```
输出：
```shell
hello
```
这次没有重写var1,$x在myfunc函数之外不再继续存在。
由上面可知，我们的问题并不是由于变量的作用域导致的。
后面在与一位同事聊天的过程中，她告我应该是脚本执行过程中开启了子shell，这样子shell里面的值是不会出现在父shell中的。根据这个思路，我进行了一些测试。
看下面这个脚本：
```shell
#!/bin/bash

cat 1.txt | while read line
do
        sleep 1000
        echo $line
done

echo $var
```
通过ps axf | grep bash
可以看到：

![bash-shell](http://7oxhal.com1.z0.glb.clouddn.com/shell.PNG)

这个时候可以发现，管道两边是子进程，也就是子shell，这应该是因为管道两边的命令是同时运行的。所以在里面的赋值，在父shell里面是不存在的，即使export也不起作用。稍稍修改一下前面的脚本就可以正确运行了，修改后的脚本：
```shell
#!/bin/bash

count=1
while read line
do
        if [ $count -eq 1 ]
        then
                s1=$line
                echo $s1
        else
                s2=$line
                echo $s2
        fi
        count=$[ $count+1 ]
done < test.data
echo $s1
echo $s2
```
输出
```shell
aaa
bbb
aaa
bbb
```
这种方式并不会开启子shell，所以变量\$s1和$s2在while外面是存在的。

通过这个问题我们来深入了解一下变量在父进程、子进程中作用域的问题以及export、source的使用。
1. 当打开一个子shell时，父shell里面中的系统环境变量会被复制到子shell中（用export定义的变量才是系统环境变量）；
2. 一个shell中的系统环境变量只对该shell或者它的子shell有效，该shell结束时变量消失，并不能返回到父shell中；
3. 不用export定义的变量只对该shell有效，对子shell也是无效的；
4. 直接执行一个脚本文件是在一个子shell中运行的，而在脚本前加source，则是在当前shell环境中直接运行（不是子shell）。

关于linux shell变量就说这么多了，欢迎交流与补充。