---
layout: post
title: EM算法
categories: 算法
date: 2018-08-18 09:47:15
tags:
    - 机器学习
    - EM
---
### 写在前面
EM算法简单来说就是，猜（E-step）,反思（M-step）,重复。
你知道一些东西（观测到的数据），你不知道一些东西（观测不到的），你就根据一些假设（parameter）先猜（E-step）,把那些不知道的东西都猜出来，假装你全都知道了；然后有了这些猜出来的数据，你反思一些，更新一下你的假设（parameter）,让你观测到的数据更加可能（Maximize likehood; M-step），然后再猜，再反思，最后，你就得到一个可以解释整个数据的假设了。
<!--more-->
### 为什么会存在EM算法
概念模型有时既含有观察遍历，又含有隐变量或潜在变量。如果概念模型的变量都是观测变量，那么给定数据，可以直接用极大似然估计法，或贝叶斯法估计模型参数，当模型含有隐变量是，EM算法就是含有隐变量的概率模型参数的极大似然估计。

### 基本思想
EM算法的基本思想是通过优化目标函数的下界，间接优化目标函数。

类似于小和尚抱小牛的故事，老和尚让小和尚从小就抱一头小牛。小牛每天长大，小和尚每天都抱得动，最后小和尚变得力大无穷。
这里小和尚的力气就是目标函数，小牛的体重就是目标函数的下界。小牛随着时间的增长而越来越重，这就是优化下界。而小和尚由于总抱小牛，力气也随着增长，这就是间接优化了目标函数。

#### EM算法
输入：观测变量数据Y，隐变量数据Z，联合分布$P(Y, Z|\theta)$，条件分布$P(Z|Y, \theta)$;
输出：模型参数$\theta$
(1)选择参数的初始值$\theta^{(0)}$，开始迭代。
(2)E步：记$\theta^{(i)}$为第$i$次迭代参数$\theta$的估计值，在第$i+1$次迭代的E步，计算：
$$Q(\theta, \theta^{(i)}) = E_{Z}[logP(Y, Z|\theta)|Y, \theta^{(i)}] = \sum_{Z}logP(Y, Z|\theta)P(Z|Y,\theta^{(i)})$$
(3)M步：求使$Q(\theta, \theta^{(i)})$极大化的$\theta$，确定第$i+1$次迭代的参数的估计值$\theta^{(i+1)}$
$$\theta^{(i+1)} =arg\ \underset{\theta}{max} Q(\theta, \theta^{(i)})$$
(4)重复第(2)步和第(3)步，直到收敛。


### 推导
通过近似求解观测数据的对数似然函数的极大问题来导出EM算法。

目标是极大化观测数据（不完全数据）Y关于参数$\theta$的对数似然函数，即极大化
$$L(\theta) = logP(Y|\theta) = log\sum_{Z}P(Y, Z|\theta) = log(\sum_{Z}P(Y|Z, \theta)P(Z|\theta))$$
这一极大化的主要困难是含有未观测数据并有包含和的对数。
通过逐步近似极大化$L(\theta)$的。假设在第$i$次迭代后$\theta$后的估计值是$\theta^{(i)}$。我们希望新估计值$\theta$能使$L(\theta)$增加，即$L(\theta) > L(\theta^{(i)})$，并逐步达到极大值。为此，考虑两者的差：
$$L(\theta) - L(\theta^{(i)}) = log(\sum_{Z}P(Y|Z, \theta)P(Z|\theta)) - logP(Y|\theta^{(i)})$$
利用Jensen不等式得到其下界：
$$\begin{equation}
\begin{aligned}
L(\theta) - L(\theta^{(i)}) &= log(\sum_{Z}P(Y|\theta^{(i)})\frac{P(Y|Z, \theta)P(Z|\theta)}{P(Y|\theta^{(i)})}) - logP(Y|\theta^{(i)}) \\
&\geq \sum_{Z}P(Z|Y, \theta^{(i)})log\frac{P(Y|Z, \theta)P(Z|\theta)}{P(Z|Y, \theta^{(i)})}) - logP(Y|\theta^{(i)}) \\
&=  \sum_{Z}P(Z|Y, \theta^{(i)})log\frac{P(Y|Z, \theta)P(Z|\theta)}{P(Z|Y, \theta^{(i)})P(Y|\theta^{(i)})}) 
\end{aligned}
\end{equation}$$
令
$$B(\theta, \theta^{(i)}) = L(\theta^{(i)}) + \sum_{Z}P(Z|Y, \theta^{(i)})log\frac{P(Y|Z, \theta)P(Z|\theta)}{P(Z|Y, \theta^{(i)})P(Y|\theta^{(i)})})$$
则
$$L(\theta) \geq B(\theta, \theta^{(i)})$$
即函数$B(\theta, \theta^{(i)})$是一个下界，任何可以使$B(\theta, \theta^{(i)})$增大的$\theta$，也可以使$L(\theta)$增大。选择$\theta^{(i+1)}$使得$B(\theta, \theta^{(i)})$达到极大，即
$$\theta^{(i+1)} = arg\ \underset{\theta}{max}B(\theta, \theta^{(i)})$$

$$\begin{equation}
\begin{aligned}
\theta^{(i+1)} &= arg\ \underset{\theta}{max}(L(\theta^{(i)}) + \sum_{Z}P(Z|Y, \theta^{(i)})log\frac{P(Y|Z, \theta)P(Z|\theta)}{P(Z|Y, \theta^{(i)})P(Y|\theta^{(i)})})) \\
&= arg\ \underset{\theta}{max}(\sum_{Z}P(Z|Y, \theta^{(i)})logP(Y|Z, \theta)P(Z|\theta))) \\
&= arg\ \underset{\theta}{max}(\sum_{Z}P(Z|Y, \theta^{(i)})logP(Y,Z | \theta))
\end{aligned}
\end{equation}
$$

### 参考文献
《统计学习方法》