---
type: principle
name: Pareto Optimality
aliases:
  - Pareto Efficiency
related:
  - Pareto Dominance
  - Pareto Frontier
---

# Pareto Optimality

> **Pareto Optimality / Pareto Efficiency · 帕累托最优 / 帕累托效率**  
> 一个方案如果已经无法在“不让任何目标变差”的前提下继续改善至少一个目标，它就是帕累托最优的。

## 核心直觉

很多问题并不存在一个在所有维度上都“最好”的答案。

例如一个系统同时追求 **延迟更低、质量更高、成本更低**。如果一个方案还能降低延迟，同时质量和成本都不变差，那么原方案显然还不够好；但当你已经走到这样一个位置：

**想继续改善任何一个目标，都必须牺牲至少另一个目标。**

这个位置就是一个 **Pareto-optimal solution**。

因此，帕累托最优描述的不是“全局最好”，而是：

> **已经消除了可以无代价获得的改进，剩下的都是 trade-off。**

---

## 三个连在一起的概念

### 1. Pareto Dominance — 帕累托支配

假设有两个方案 **A** 和 **B**。

如果：

1. A 在所有目标上都不差于 B；
2. 并且至少有一个目标严格优于 B；

那么称 **A Pareto-dominates B（A 帕累托支配 B）**。

因此 B 是一个 **dominated solution（被支配解）**：存在另一个方案可以在没有任何代价的情况下替代它。

### 2. Non-dominated Solution — 非支配解

如果不存在任何其他可行方案能够支配 A，那么 A 是一个 **non-dominated solution**。

在通常的多目标优化语境中，这些非支配解就是 **Pareto-optimal solutions**。

### 3. Pareto Frontier / Pareto Front — 帕累托前沿

把所有 Pareto-optimal solutions 放在一起，就形成 **Pareto Frontier（帕累托前沿）**。

可以把它想象成多目标空间中的一条“有效边界”：前沿之外、被其他方案全面压过的点没有选择价值；而前沿上的点彼此之间通常不存在无代价的胜负。

因此真正需要做的决策往往不是“哪个方案最好”，而是：

> **我们愿意用多少 X，去交换多少 Y？**

---

## 来源

这一概念以 **Vilfredo Pareto（1848–1923）** 命名。他是意大利经济学家、社会学家和工程师，其关于经济效率和福利的研究奠定了后来 **Pareto efficiency / Pareto optimality** 的思想基础。

后来这一思想从福利经济学扩展到了多目标优化（multi-objective optimization）、运筹学（operations research）、工程设计、博弈论以及系统与产品决策。

## 为什么值得记住

Pareto 思维最有价值的地方，是帮助区分两类问题：**还有明显劣解可以淘汰**，还是 **已经进入真正的 trade-off 区域**。

当系统设计中的多个目标彼此冲突时，先寻找 Pareto frontier，往往比过早争论一个单一的“最佳方案”更有意义。
