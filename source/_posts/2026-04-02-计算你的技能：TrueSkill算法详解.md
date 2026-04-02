---
title: 计算你的技能：TrueSkill算法详解
date: 2026-04-02 20:54:00
tags:
  - 游戏
  - 算法
  - 机器学习
  - TrueSkill
  - Elo
categories:
  - 技术
---

说实话，作为一个对游戏匹配系统感兴趣的玩家，我一直想深入了解这些看似玄学的分数背后的数学原理。这篇文章是Jeffrey Moser的经典之作，原文发表于2010年，讲解了TrueSkill算法的工作原理。翻译这篇文章，希望能帮助更多人理解这个优雅的贝叶斯评分系统。

<!-- more -->

## 引言

事情看起来很简单：我想创建一个数据库来追踪同事们在国际象棋和桌上足球方面的技能水平。我知道自己桌上足球玩得不好，会拖累高手们。我很好奇是否有一种算法能更好地创建平衡的比赛。同时，我也想知道自己国际象棋是否有进步。

我知道需要一个简单的方法来收集每个人的比赛结果，然后用一种能随着数据增加而不断改进的算法。根据我之前听说过的一些东西，这似乎很适合用"机器学习"来解决。

但是，机器学习是计算机科学的热门领域——但它令人生畏。像大多数学科一样，要成为该领域的专家需要学习很多东西。我不需要深入研究，只需要理解足够的知识来解决我的问题。Jamie Zawinski说得好：

> "不知道某件事并不意味着你笨——只是意味着你不知道它。"

## 技能 ≈ 获胜概率

技能很难衡量。在某件事上做得好需要刻意的练习，有时候还需要一点运气。如何衡量一个人的技能？你可以问一个人他是否有技能，但这只能给出一个粗略的近似值，因为人们往往对自己的能力过度自信。

![100米短跑](/images/truetkSkill/100M_dash_Osaka07_D2A_Torri_Edwards_320.jpg)

也许更好的问题是："技能的单位是什么？"对于100米短跑这样的项目，你可以简单地取最近几次冲刺的秒数平均值。

![硬币实验](/images/truetkSkill/pennyheads_400.jpg)

然而，对于国际象棋这样的游戏，更难衡量，因为真正重要的只是你是否赢、输或平。

也许只统计胜负总数是有道理的，但这对玩得多（或玩得少）的人不公平。稍微好一点的是记录你赢的比赛百分比。然而，这对欺负弱者和可能被击败但也许学到了一些东西的玩家不公平。

![硬币正面概率](/images/truetkSkill/headspercentage_576.png)

理想情况下，如果所有玩家大约赢一半的比赛，我们会说比赛是平衡的。在这种理想情况下，每个人都会有接近50%的胜率。

## 什么是概率？

概率是一个有争议的概念。一种观点是：概率描述了你在进行无数次实验后期望看到的东西。例如，如果你抛无数次硬币，你期望大约一半正面朝上。这被称为**频率学派**观点。

另一种观点是：概率衡量你多么确信某事是真的。例如，如果你根据一些证据非常确定某张牌是A，那么你说这发生的概率是80%。这是**贝叶斯观点**。对于技能评分，我们使用贝叶斯观点。

![扔硬币结果随次数变化](/images/truetkSkill/totalheads1_576.png)

随着实验次数增加，正面比例趋向50%

![多次硬币实验结果](/images/truetkSkill/totalheads10_576.png)

很多次实验后，比例稳定在50%附近

在数学上，我们假设存在一个"真实"的技能数字，但这个数字被一些我们无法控制的随机噪声"污染"了。这个噪声可能来自疲劳、注意力不集中、运气或你意想不到的走法。

![正态分布](/images/truetkSkill/10_DM_Gauss_Cropped_576.jpg)

高斯（Gauss）和他的正态分布——统计学中最重要的分布之一

## 正态分布

在数学上，我们通常假设噪声服从**正态分布**（也称为高斯分布）。正态分布是钟形曲线的另一个名称。它是统计学中最重要的分布之一。

![正态分布曲线](/images/truetkSkill/NormalDistributionWithPercentages_576.png)

正态分布由两个数字完全描述：**均值（μ）**和**方差（σ²）**。均值是你期望看到的平均值。方差衡量结果的变化程度。

![3D高斯分布](/images/truetkSkill/Gaussian_3D_Circular.png)

对于技能评分，我们用同样的方法。每个玩家都有一个技能均值（μ）和一个技能方差（σ²）。

![2D等高线](/images/truetkSkill/Gaussian_2D_Contour_640.png)

## Elo评分系统

为了介绍技能评分系统的历史，我们从Elo评分系统开始。Elo最初是为国际象棋设计的，但后来被许多1v1游戏采用。

![国际象棋](/images/truetkSkill/ChessSet_160.jpg)

在Elo中，每个玩家有一个技能数字。当两个玩家比赛时，Elo使用这个公式计算预期分数：

**E[A对B] = 1 / (1 + 10^((R[B] - R[A]) / 400))**

直觉上，评分差异越大，预期分数越极端。如果两个玩家的评分相同，预期分数是0.5（50%）。

比赛后，使用这个公式更新评分：

**R'[A] = R[A] + K × (S[A] - E[A对B])**

直觉上，如果你赢了（实际表现超过预期），你的评分会增加。如果你的实际表现不如预期，你的评分会下降。

![Elo更新前后对比](/images/truetkSkill/bell_curves_of_bright_beginner_vs_jeff_before_576.png)

比赛前：绿色虚线是我的技能分布，蓝色实线是一个初学者的分布

![Elo更新后差异](/images/truetkSkill/bell_curves_difference_576.png)

比赛后，两个技能分布的差异更加明显

![胜率与评分差异](/images/truetkSkill/cdf_chess_given_rating_difference_640.png)

## Elo的局限性

Elo是一个优雅的系统，但它有几个局限性：

**新玩家问题**：对于新玩家，我们应该期待什么评分？如果他们在前几场比赛中运气不好，评分可能会很低。

**组队游戏**：Elo是为1v1设计的。在组队游戏中，你如何衡量每个人对团队胜利的贡献？

**平局处理**：在某些游戏中平局很常见。Elo处理平局的方式有些笨拙。

**不确定性**：Elo只给你一个数字——没有不确定性度量。

![K因子影响](/images/truetkSkill/KFactorAlphaImpact_576.png)

K因子对新玩家影响更大，可以更快地调整他们的评分

## TrueSkill登场

TrueSkill是由微软研究院的Ralf Herbrich、Tom Minka和Thore Graepel设计的，目的是解决Elo的这些局限性。TrueSkill使用贝叶斯推断来根据比赛结果更新技能估计。

在TrueSkill中，每个玩家有：
- 一个技能均值（μ）
- 一个技能标准差（σ）

初始值通常是μ = 25，σ = 8.33。这对应于"不确定性很大"的意思。

![TrueSkill更新前后](/images/truetkSkill/TrueSkillCurvesBeforeExample_576.png)

比赛前

![TrueSkill更新后](/images/truetkSkill/TrueSkillCurvesAfterExample_576.png)

比赛后：获胜者的μ增加（红色曲线右移），失败者的μ减少

## 贝叶斯推断入门

在深入TrueSkill之前，我们需要了解一些背景知识。

![贝叶斯公式](/images/truetkSkill/Thomas_Bayes_220.gif)

Thomas Bayes (1702-1761)——贝叶斯推断的创始人

贝叶斯推断的核心思想是：我们从一个先验分布开始，然后根据新的证据（数据）更新它，得到后验分布。

![3D先验分布](/images/truetkSkill/Gaussian_3D_Circular_576.png)

先验分布

![3D似然分布](/images/truetkSkill/Gaussian_3D_Likelihood_576.png)

似然分布——根据观察到的结果

![3D后验分布](/images/truetkSkill/Gaussian_3D_Posterior_576.png)

后验分布——先验和似然的结合

## 2D情况下的贝叶斯更新

假设我们有两个玩家：Alice和Bob。我们想根据他们之间的比赛结果更新他们的技能估计。

![2D先验](/images/truetkSkill/Gaussian_2D_Prior_576.png)

两人技能的初始联合分布

![2D似然](/images/truetkSkill/Gaussian_2D_Likelihood_576.png)

根据Bob获胜的证据，似然分布

![2D后验](/images/truetkSkill/Gaussian_2D_Posterior_576.png)

结合后，得到两人技能的后验分布

如果Alice赢了而不是Bob，似然分布会向相反方向移动：

![2D似然（相反方向）](/images/truetkSkill/Gaussian_2D_Likelihood_Opposite_Direction_576.png)

![2D后验（更新后）](/images/truetkSkill/Gaussian_2D_Posterior_Updated_576.png)

## 因子图

TrueSkill使用一种叫做**因子图**的东西来表示计算。因子图是一种可视化复杂概率计算的方法。

![复杂因子图](/images/truetkSkill/factor_graph_complicated_factorization_576.png)

复杂的因子分解

![简化因子图](/images/truetkSkill/factor_graph_complicated_simplified_576.png)

简化的因子图

在TrueSkill的因子图中，有三种类型的节点：
1. **玩家节点**：每个玩家一个
2. **团队技能节点**：每个团队一个
3. **结果节点**：比赛的结果

![TrueSkill完整因子图](/images/truetkSkill/TrueSkillFullFactorgraph_720.png)

TrueSkill的完整因子图

## 消息传递

在因子图上，TrueSkill使用消息传递算法来计算后验分布。

**第一层：先验**

![第一层：先验](/images/truetkSkill/Layer1_priors_576.png)

每个玩家从先验分布开始（μ=25, σ=8.33）

**第二层：似然**

![第二层：似然](/images/truetkSkill/Layer2_likelihood_576.png)

根据比赛结果计算似然分布

**第三层：团队求和**

![第三层：团队求和](/images/truetkSkill/Layer3_team_sum_576.png)

团队技能是团队中所有成员技能的总和

**第四层：团队差异**

![第四层：团队差异](/images/truetkSkill/Layer4_Team_Diff_576.png)

比赛结果取决于团队技能差异

**第五层：差异比较**

![第五层：差异比较](/images/truetkSkill/Layer5_Diff_Comparison.png)

比较技能差异以确定获胜者

## V和W函数

TrueSkill使用两个特殊函数来处理比赛结果：

**V函数**：给定团队差异，计算获胜概率

![V函数](/images/truetkSkill/VWinFunctionWithDrawProbabilities_576.png)

**W函数**：用于计算更新的权重

![W函数](/images/truetkSkill/WWinFunctionWithDrawProbabilities_576.png)

## 迭代更新

经过几次迭代后，消息会收敛到稳定的值。

![迭代更新过程](/images/truetkSkill/Layer_Iterate_Inner_576.png)

## 结果

实现TrueSkill算法最有益的部分是看到它在实践中运行良好。

![桌上足球TrueSkill排行榜](/images/truetkSkill/MostRecentFoosballTrueSkill_720.png)

我的同事们评论说，它在相对快速地计算每个人的正确技能方面几乎是"诡异"地准确。

看看系统有多快地检测到新玩家有多好，即使他的胜率正好在50%左右，这是很有趣的。

## 结论

TrueSkill是一个优雅的贝叶斯技能评分系统，它：

- **处理新玩家**：快速了解新玩家的真实技能水平
- **处理组队游戏**：衡量每个人对团队胜利的贡献
- **处理平局**：优雅地处理平局情况
- **不确定性量化**：告诉你对技能估计有多少信心

理解TrueSkill不仅能帮你更好地认识游戏匹配逻辑，还能让你了解贝叶斯推断在实际应用中的威力。

---

原文：[Computing Your Skill](https://www.moserware.com/2010/03/computing-your-skill.html) by Jeffrey Moser  
翻译：小川

参考链接：
- [The Math Behind TrueSkill](https://www.moserware.com/assets/computing-your-skill/The%20Math%20Behind%20TrueSkill.pdf) - 详细的数学推导论文
- [Moserware.Skills](https://github.com/moserware/Skills) - GitHub上的开源实现
- [Microsoft's online TrueSkill Calculators](https://www.microsoft.com/en-us/research/project/trueskill/) - 在线计算器
