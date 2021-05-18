---
layout: post
title: Google OR-Tools 搜索参数、源码编译、cython实现及一些使用Tips 
categories: 算法
date: 2021-05-18 20:49:58
tags:
    - OR-Tools
    - 运筹学
    - 整数规划
---
### OR-Tools CP-SAT 搜索参数

所有参数:

https://github.com/google/or-tools/blob/stable/ortools/sat/sat_parameters.proto

很多参数都要在了解背后的搜索算法后，才知道含义

输出所有参数及其默认值：
```python
attributes = [attr for attr in dir(solver.parameters) if not attr.startswith('__')]
for attr in attributes:
    if attr == "Extensions":
        continue
    print("{} = {}".format(attr, eval("solver.parameters.{}".format(attr))))
```

**下面是参数类型及一些例子：**

#### Branching and polarity 搜索算法相关参数
```python
enum VariableOrder {
    IN_ORDER = 0;  // As specified by the problem.
    IN_REVERSE_ORDER = 1;
    IN_RANDOM_ORDER = 2;
}

# 变量顺序
# 这个参数不变，可以在定义变量时，考虑变量顺序
preferred_variable_order = IN_ORDER

enum Polarity {
    POLARITY_TRUE = 0;
    POLARITY_FALSE = 1;
    POLARITY_RANDOM = 2;
    POLARITY_REVERSE_WEIGHTED_SIGN = 4;
  }
  
# 初始化相关设置
optional Polarity initial_polarity = POLARITY_FALSE;
```

#### Conflict analysis 冲突分析相关参数
```python
enum ConflictMinimizationAlgorithm {
    NONE = 0;
    SIMPLE = 1;
    RECURSIVE = 2;
    EXPERIMENTAL = 3;
  }

# 最小化冲突算法
optional ConflictMinimizationAlgorithm minimization_algorithm = RECURSIVE;

```

#### Clause database management 子句数据管理
```python
# 触发清理，当可删除子句数量达到下面的配置
clause_cleanup_period = 10000;
```

#### Variable and clause activities 变量子句活动设置
当每个冲突找到的时候，变量的活动开始增加
```python
variable_activity_decay = 0.8
```

#### Restart 算法重启相关配置
主要是配置在什么情况会重启算法
```python
enum RestartAlgorithm {
    NO_RESTART = 0;
    
    LUBY_RESTART = 1;
    
    DL_MOVING_AVERAGE_RESTART = 2;

    LBD_MOVING_AVERAGE_RESTART = 3;

    FIXED_RESTART = 4;
  }

default_restart_algorithms = "LUBY_RESTART,LBD_MOVING_AVERAGE_RESTART,DL_MOVING_AVERAGE_RESTART";
```

#### Limits 搜索限制相关配置
```python
# 最大搜索时间，默认是不限制
# 在Solver()调用的时候开始计算
max_time_in_seconds = inf

# 直接配置好像不起作用
max_number_of_conflicts = 0x7FFFFFFFFFFFFFFF

# 单线程求解器的最大内存限制，超过限制求解器会中止
max_memory_in_mb = 10000

# 不是太清楚这个参数
# 最好的可行解 O 和 最好的目标边界 B
# - Absolute: abs(O - B)
# - Relative: abs(O - B) / max(1, abs(O)).
absolute_gap_limit = 0.0
relative_gap_limit = 0.0
```

#### Other 其他参数
```python
# 求解器随机数种子，可以考虑改变
random_seed = 1

# 是否输出搜索过程日志, 默认日志级别INFO，可通过参数log_destination改变
log_search_progress = false

# 日志前缀
log_prefix = ""

# 日志是否输出到控制台
# 整合到系统中，可设置会false
log_to_stdout = True

# 是否输出response proto
# 建议true，可以看到搜索的详细结果
log_to_response = false 
```

#### Presolve 阶段相关参数
主要是在搜索前，对model做一些处理，有多种presolve策略（对变量、约束），以加速后续的搜索

Presolve参数过程可以参考：[How the CP-SAT solver works](https://www.xiang.dev/explaining-cp-sat/)

![image.png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_ortools_1.png)

这个阶段对问题规模有一定要求，在较大规模问题下，开启这个应该有加速效果，在较小规模下开启应该会增加时间
```python
# 边界变量消除，变量x，not(x)出现的次数小于这个参数
presolve_bve_threshold = 500

# 是否在presolve阶段使用BVA (Bounded Variable Addition)
presolve_use_bva = 72

# presolve 阶段，最大迭代次数
max_presolve_iterations = 3

# 是否在搜索前使用presolve，默认为True
cp_model_presolve = true

# 高级用法，其实有两套presolve代码，默认是一种
cp_model_postsolve_with_full_solver = false

# 如果是正值，在多种presolve策略后，会尝试停止，方便debug presolve阶段
cp_model_max_num_presolve_operations = 0

还有一些其他的参数，可设置参数很多...
```

设置cp_model_presolve = false后，速度提升比较明显，不会等待最长超时时间

在n_queens上试验，n=11, 相较开启presolve, 有1s左右的时间提升

#### Max-sat 参数
```python
# 是否使用一些提示/暗示来找到一个较好的初始解
use_optimization_hints = true

```

#### Constraint programming 约束规划参数
```python
# 代表考虑的约束类型，匹配系统中应该设置为2
# 0: no LP relaxation is used
# 1: only the linear constraint and full encoding are added
# 2: we also add all the Boolean constraints.
linearization_level = 1

# 停止求解，只要有一个可行解
# 可尝试设置
stop_after_first_solution = false

# 在presolve 阶段后停止
stop_after_presolve = false

# 并行化搜索的线程数，默认为1
num_search_workers = 1

# false，表示各个线程搜索是独立的
# true，多个线程当成一个整体，会分发搜索策略
interleave_search  = false
```


### 源码编译 & cython相关

环境：ubuntu 18.04 & Python 3.6

#### 调用第三方求解器

OR-Tools中内置一些求解器，如MP Solver和CP-SAT solver。

但是OR-Tools实际上提供的是统一的求解器接口，内部连接的具体求解器我们可以配置，支持以下：
> * SCIP 默认整合，无需手动安装
> * Gurobi
> * GLKP (Linux and MacOS only)
> * CPLEX: IBM开发的一个商用求解器

调用第三方求解器时，需要单独安装第三方求解器，同时or-tools需要源码安装。

安装流程可以参考官方文档[https://developers.google.com/optimization/install/python/source_linux](https://developers.google.com/optimization/install/python/source_linux)中**Build third parties**，以及相关资料中的文档。

下载stable分支源码后，执行`make third_party`报错如下：

![image.png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_ortools_2.png)

移除makefiles/Makefile.third_party.unix.mk 210，283行-v参数，-v应该是输出，可能不支持了，移除应该不影响。

也可能是cmake版本问题.

第三方求解器源码编译时间较长(只是编译默认开源求解器scip、CBC等，添加其他可能时间更长)。

编译成功：

![image.png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_ortools_3.png)

可行性较高。可以在不用修改原有代码的情况下，使用各种第三方求解器。


#### cython相关

1、源码编译or-tools
```shell
make cc
```

编译成功：

![image.png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_ortools_4.png)

编译后的库文件：

![image.png](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_ortools_5.png)

2、编写增加约束cython代码，xx.pyx文件

定义包装器，用来桥接Python解释器到C++代码

需要对c++代码，函数有一定了解

用cython代码写添加约束的逻辑，生成函数

类似如下的函数。

```python
def MakeMatrixConstraint(solver, coefficients, lin_expr, double[:] lb, double[:] ub):
    # Make sure it is the right SwigPyObject
    assert isinstance(solver, Solver)
    assert coefficients.shape[0] == lb.shape[0]
    assert coefficients.shape[0] == ub.shape[0]
    assert coefficients.shape[1] == len(lin_expr)
    
    # extract MPSolver from SWIG
    cdef SwigPyObject* swig_obj = <SwigPyObject*?>solver.this
    cdef MPSolver* solver_ptr = <MPSolver*>swig_obj.ptr

    # Extract the linear expression
    cdef vector[LinExpr] vec_lin_expr
    for x in lin_expr:
        vec_lin_expr.push_back(pycoefs_to_linexpr(CastToLinExp(x).GetCoeffs()))

    if isspmatrix(coefficients):
        A = coefficients if coefficients.format == 'coo' else coo_matrix(coefficients)
        A.sum_duplicates()
        idx = argsort(A.row)
        if A.data.dtype == float64:
            return MakeMatrixConstraintSparse64(solver_ptr, A.data[idx], A.col[idx], A.row[idx], vec_lin_expr, lb, ub)
        else:
            return MakeMatrixConstraintSparse32(solver_ptr, A.data[idx], A.col[idx], A.row[idx], vec_lin_expr, lb, ub)
    else:
        # call internal function
        if coefficients.dtype == float64:
            return MakeMatrixConstraintDense64(solver_ptr, coefficients, vec_lin_expr, lb, ub)
        else:
            return MakeMatrixConstraintDense32(solver_ptr, coefficients, vec_lin_expr, lb, ub)
```
3、编写setup.py函数，构建扩展模块
```
from distutils.core import setup
from distutils.extension import Extension
from Cython.Distutils import build_ext

ext_modules = [
    Extension('sample',

              ['sample.pyx'],
              libraries=['sample'],
              library_dirs=['.'])]
setup(
  name = 'Sample extension module',
  cmdclass = {'build_ext': build_ext},
  ext_modules = ext_modules
)
```

然后就可以在python代码中使用了。

可以尝试cython的方式，但是感觉比较麻烦。

**其实可以考虑整数规划整体用C\+\+实现，然后使用C\+\+ grpc开放端口，供线上服务调用。**

### Tips

**1. 减少变量数量 & 减少整数变量的域**


**2. 首选bool型而不是整数变量/约束**

其实从源码来看差不多，bool型变量也是使用的IntVar，只是域是（0， 1）

其实定义bool变量是，可以直接使用IntVal(0, 1)，但要考虑有些方法只针对bool型变量

```python
def NewBoolVar(self, name):
    """Creates a 0-1 variable with the given name."""
    return IntVar(self.__model, Domain(0, 1), name)
```

**3. 如何只寻找可行方案**
```
solver.SolveWithSolutionCallback()
```
SearchForAllSolutions 有存储过程

**4. 查看模型的一些信息**
```python
print(model.ModelStats())
```

**5. Parallelization（并行化）**

可以使用多个线程并行运行求解器求解

```python
solver = cp_model.CpSolver()
solver.parameters.num_search_workers = 8 #CPU内核数
```

应该是前6个线程使用前6个搜索算法，其余的都是使用LNS，可能参数不同或者随机种子不同
> * Default SAT Search
> * Fixed search or LP Branching
> * PSEUDO_COST_SEARCH (follow last best solution when branching)
> * No linear relaxation (good for big models where CP-SAT propagation is enough)
> * Max linear relaxation (gives good lower bounds)
> * Core Based search
> * LNS for remaining workers

搜索是独立的，存在解重复，可以尝试通过下面这个参数避免：
```python
interleave_search  = True
```

**6. 日志开启**

程序崩溃，或者搜索没有结果时，可以查看日志。

```python
log_search_progress = True
```

可设置其他相关日志参数

和系统整合是，要考虑日志的兼容性


**7. Solution hint / Warm start(解决方案提示/热启动)**

如果我们在求解过程中加入一些变量的提示则可能加快求解速度。
```python
num_vals = 3
x = model.NewIntVar(0, num_vals - 1, 'x')
y = model.NewIntVar(0, num_vals - 1, 'y')
z = model.NewIntVar(0, num_vals - 1, 'z')
 
model.Add(x != y)
 
model.Maximize(x + 2 * y + 3 * z)
 
# Solution hinting: x <- 1, y <- 2
model.AddHint(x, 1)
model.AddHint(y, 2)
```

**8. 单向约束**

Implications方法是一种单向的约束操作:a => b (a,b均为布尔型变量) ，a为True则b也为True,反之则不成立
```python
model.AddImplication(a, b)
```

**9. 推荐使用字典来存储多索引变量**

Storing Multi-index variables，便于理解

**10. 强制所有变量都不相同约束**
```python
model.AddAddAllDifferent(var_arr)
```

**11. 元素约束**
```python
# target == var_arr[index]
 
model.AddElement(index, var_arr, target)
```

**12. 优化目标，多目标优化**

可以使用带权重的优化目标，比如5 * a + 2 * b -c，权重代表了优化目标的优先级顺序,权重越大则优先级越高。在使用最大化（最小化）优化时，希望其中的一个目标最小化（最大化），可以给该目标赋一个负值权重

此时尽量不要使用SearchForAllSolutions方法来搜索所有的解

```python
model.Minimize(5*a+2*b-c)
```

**13. Domain**

在创建多个变量时，使用Domain应该比单独创建要快

```python
domain = cp_model.Domain(0, 10)
domain = cp_model.Domain.FromValues([1, 3, 4, 6])
domain = cp_model.Domain.FromIntervals([[1, 2], [4, 6]])
domain = cp_model.Domain.FromIntervals([
    [cp_model.INT_MIN, -2],
    [2, cp_model.INT_MAX]
])
```


### 相关资料

[http://www.swig.org/](http://www.swig.org/)

[利用 SWIG 对 C++ 库进行 Python 包装](https://segmentfault.com/a/1190000013219667)

[用SWIG包装C代码](https://python3-cookbook.readthedocs.io/zh_CN/latest/c15/p09_wrap_c_code_with_swig.html)

[线性规划之Google OR-Tools 简介与实战](https://blog.csdn.net/weixin_42608414/article/details/109556196)

[Recipes for Google OR-Tools](https://www.xiang.dev/cp-sat/#)

[你所不知的角落，有人在做没有深度学习的AI](https://www.aminer.cn/research_report/5f26ace921d8d82f52e5b141)

[https://github.com/google/or-tools/blob/stable/ortools/sat/doc/model.md](https://github.com/google/or-tools/blob/stable/ortools/sat/doc/model.md)

[How the CP-SAT solver works](https://www.xiang.dev/explaining-cp-sat/)

[分支定界](https://www.cnblogs.com/xiaofanke/p/9498820.html#:~:text=%E5%88%86%E6%94%AF%E5%AE%9A%E7%95%8C%E6%B3%95%EF%BC%88branch,%E5%8F%98%E9%87%8F%E5%92%8C%E5%AD%90%E9%97%AE%E9%A2%98%E8%BF%9B%E8%A1%8C%E5%88%86%E6%94%AF%E3%80%82)

[cplex求解整数规划_Ortools调用第三方求解器](https://blog.csdn.net/weixin_39845430/article/details/113052882)

[https://www.jianshu.com/p/da3bf1b47681](https://www.jianshu.com/p/da3bf1b47681)

[Install ortools with Gurobi solver in python venv #2041](https://github.com/google/or-tools/issues/2041)

[用Cython包装C代码](https://python3-cookbook.readthedocs.io/zh_CN/latest/c15/p10_wrap_existing_c_code_with_cython.html)

[Python: Add many constraints more efficiently](https://github.com/google/or-tools/issues/1020)