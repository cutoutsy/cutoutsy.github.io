---

layout: post
title: OR-Tools入门教程
categories: 算法
date: 2022-08-05 15:37:49
tags:
    - OR-Tools
    - 运筹学
    - 组合优化
---
OR-Tools是用c++编写的，但也可以在Python、Java或C#中使用它。
下面主要介绍使用Python创建和运行OR-Tools程序。

以下部分将帮助您开始使用Python的OR-Tools:

* 什么是优化问题
* 使用Python解决优化问题
* 更多使用Python的例子
* 确定你希望解决的问题类型

### 什么是优化问题

优化的目标是从一大堆可能的解决方案中找到一个问题的最佳解决方案。（有时你会满足于找到任何可行的
解决方案；OR-Tools可以做到这一点）

这里有一个典型的优化问题。假设一家运输公司使用卡车车队向客户运送包裹。每天，该公司必须将包裹分配给卡车，然后为每辆卡车选择运送包裹的路线。每一个包裹和路线的分配都一个成本，基于卡车的总行驶距离或者其他因素。问题是选择成本最低的包裹和线路分配方案。

与所有优化问题一样，这个问题包含以下元素：

* **优化目标**-想要优化的数值。在上面的例子中，目标是最小化成本。为了建立一个优化问题，你需要定义一个函数来计算解决方案的优化数值。这个函数称作目标函数。在前面的例子中，目标函数将计算任意分配包裹和路线方案的总成本。
  
  最优解是目标函数最佳的解。（最佳可以是最大值，也可以是最小值）
* **约束**-根据问题的具体要求，限制可能解的集合。例如，如果运输公司不能分配包裹给超过卡车载重，这将对解决方案施加约束。可行解是满足问题的所有给定约束条件，而不一定是最优解。

求解优化问题的第一步是确定目标和约束条件。

### 使用Python解决优化问题
接下来，我们给出一个优化问题的例子，并展示如何用Python建立和解决它。

#### 线性优化问题
在优化领域，线性优化（或线性规划）是最古老和最广泛使用的问题之一，其中其目标函数和约束可以写成线性表达式。下面式这类问题的一个简单例子。

**在一下约束条件下最大化**$3x + y$：

$$ 0 \leq x \leq 1 $$

$$ 0 \leq y \leq 2 $$

$$  x + y \leq 2 $$

在这个例子里目标函数是$3x + y$。目标函数和约束都是由线性表达式给出的，这使得这是一个线性问题。

#### 求解问题的主要步骤
对于每种编程语言，建立和解决问题的基本步骤都是相同的：

* 导入必需的依赖库
* 声明求解器
* 创建变量
* 定义约束
* 定义目标函数
* 调用求解器并显示结果

#### Python编程
本节将介绍通过Python编程来建立和求解问题

> **Note**
> 完整的程序和运行它的步骤显示在本节的最后

以下是步骤：

**导入必需的依赖库**
```python
from ortools.linear_solver import pywraplp
from ortools.init import pywrapinit
```
**声明求解器**
```python
# 创建一个基于GLOP后端的线性求解器
solver = pywraplp.Solver.CreateSolver('GLOP')
```
`pywraplp`是底层C++求解器的Python包装器。参数`"GLOP"`指定了OR-Tools线性求解器[GLOP]()

**创建变量**
```python
# 创建变量x, y
x = solver.NumVar(0, 1, 'x')
y = solver.NumVar(0, 2, 'y')

print('Number of variables =', solver.NumVariables())
```
**定义约束**

前两个约束，$0 \leq x \leq 1$和$0 \leq y \leq 2$已经在变量中定义了。下面的代码是定义约束
$x + y \leq 2$：
```python
# 创建线性约束, 0 <= x + y <= 2
ct = solver.Constraint(0, 2, 'ct')
ct.SetCoefficient(x, 1)
ct.SetCoefficient(y, 1)

print('Number of constraints =', solver.NumConstraints())
```
方法`SetCoefficient`设置在约束表达式中x和y的系数。

**定义目标函数**
```python
# 创建目标函数, 3 * x + y
objective = solver.Objective()
objective.SetCoefficient(x, 3)
objective.SetCoefficient(y, 1)
objective.SetMaximization()
```
方法`SetMaximization`表明这是一个最大化优化问题

**调用求解器并显示结果**
```python
solver.Solve()
print('Solution:')
print('Objective value =', objective.Value())
print('x =', x.solution_value())
print('y =', y.solution_value())
```

#### 完整程序
完整的程序如下所示。
```python
from ortools.linear_solver import pywraplp
from ortools.init import pywrapinit


def main():
    # 创建一个基于GLOP后端的线性求解器
    solver = pywraplp.Solver.CreateSolver('GLOP')

    # Create the variables x and y.
    x = solver.NumVar(0, 1, 'x')
    y = solver.NumVar(0, 2, 'y')

    print('Number of variables =', solver.NumVariables())

    # 创建线性约束, 0 <= x + y <= 2
    ct = solver.Constraint(0, 2, 'ct')
    ct.SetCoefficient(x, 1)
    ct.SetCoefficient(y, 1)

    print('Number of constraints =', solver.NumConstraints())

    # 创建目标函数, 3 * x + y
    objective = solver.Objective()
    objective.SetCoefficient(x, 3)
    objective.SetCoefficient(y, 1)
    objective.SetMaximization()

    solver.Solve()

    print('Solution:')
    print('Objective value =', objective.Value())
    print('x =', x.solution_value())
    print('y =', y.solution_value())


if __name__ == '__main__':
    pywrapinit.CppBridge.InitLogging('basic_example.py')
    cpp_flags = pywrapinit.CppFlags()
    cpp_flags.logtostderr = True
    cpp_flags.log_prefix = False
    pywrapinit.CppBridge.SetFlags(cpp_flags)

    main()
```
#### 运行程序
你可以按照如下方式运行上面的程序：
1. 复制粘贴上面的代码到一个新文件中，并保存为`program.py`
2. 打开命令行窗口，并切换到保存`program.py`的目录
3. 在命令提示符下，输入
```shell
python relative/path/to/program.py
```
其中*relative/path/to/*是保存程序的目录的路径

程序返回最大化目标函数的x和y:
```
Solution:
x =  1.0
y =  1.0
```
### 更多Python示例
有关如何解决各种类型优化问题的更多Python示例，情参加[示例]()

### 确定你希望解决的问题类型
世界上有各种各样的优化问题。对于每种类型的问题，都有不同的方法和算法来寻找最优解。在你开始编写解决优化问题之前，你需要确定你正在处理的问题类型，然后选择一个合适的*求解器*-一种寻找最佳解决方案的算法。

下面你将看到OR-Tools解决的问题类型的简要概述，以及如何解决每种问题的链接。
* [线性优化]()
* [约束优化]()
* [混合整数优化]()
* [装箱]()
* [网络流]()
* [分配问题]()
* [调度问题]()
* [路径规划]()

#### 线性优化
正如在前面学到的，线性优化问题是目标函数和约束都是线性表达式的一类问题。OR-Tools中用于解决这类问题的主要求解器是线性优化求解器，它实际上是对几个用于线性和混合整数规划求解器的封装，包括第三方求解器。

[了解更多关于线性优化]()

#### 约束优化
约束优化，或约束规划（CP），问题可以建模在任意约束条件下，从一个非常大的候选集中，确定可行的解决方案。CP基于可行性（寻找可行解）而不是优化（寻找最优解），关注约束和变量而不是目标函数。然后，CP可以用来解决优化问题，只需简单的比较所有可行解的目标函数数值。

[了解更多关于约束优化]()

#### 混合整数规划
*混合整数优化问题*是要求某些或者所有变量都是整数的一类问题。比如分配问题，一组工人需要被分配到一组任务。对于每个工人和任务，定义一个变量，如果给该工作者分配该任务，则该变量值为1，否则为0。在本例中，变量只能取0或1。

[了解更多关于混合整数规划]()

#### 装箱问题
*装箱问题*是将一组不同尺寸的物品装入不同容量的容器的一类问题。其目标是根据容器的容量，装尽可能多的物品。一个特殊的例子是*背包问题*，其中只有一个容器。

[了解更多关于装箱问题]()

#### 网络流
许多问题可以用节点和节点之间的有向边组成的有向图来表示。例如，运输问题，货物通过铁路网运输，可以用图来表示，其中边是铁路线，节点是配送中心。在*最大流问题*中，每条边都有一个最大运输能力。问题是分配每条边运输货物的数量，使得总运输量尽可能大。

[了解更多关于网络流]()

#### 分配问题
*分配问题*是将一组工人或者机器分配给一组任务，其中每个工作或者机器在不同的任务上有固定的成本。问题是找到总成本最小的分配方案。分配问题实际上是网络流问题的一种特殊情况。

[了解更多关于分配问题]()

#### 调度问题
*调度问题*涉及分配资源以在特定时间执行一组任务。一个重要的例子是*作业车间调度问题*，其中多个作业在多台机器上处理。每个作业由一系列任务组成，这些任务必须按照给定的顺序执行，每个任务必须在特定的机器上处理。问题是分配一个时间表，以便在尽可能短的时间内完成所有工作。

[了解更多关于调度问题]()

#### 路径规划
路径规划涉及寻找通过网络的最优路线车队，网络是一个有向图。问题是将包裹分配给运输卡车，见[什么是优化问题]()描述，是路径规划的一个例子。另一个是[旅行推销员问题]()

[了解更多关于路径规划]()
