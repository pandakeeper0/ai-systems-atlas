---
type: math-note
domain: mathematics
section: calculus
name: Derivative
chinese_name: 导数
---

# Derivative · 导数

> **核心直觉：导数不是一个凭空出现的公式。它是“割线斜率”在两个点无限靠近时的极限。**

## 1. 从一个真正的问题开始

设有函数 $y=f(x)$。我们想知道它在某个点 $x$ **变化得有多快**。

如果取两个不同的点 $x$ 和 $x+\Delta x$，对应函数值为 $f(x)$ 和 $f(x+\Delta x)$，那么这段区间上的平均变化率是：

$$
\frac{\Delta y}{\Delta x}=\frac{f(x+\Delta x)-f(x)}{\Delta x}
$$

几何上，这就是曲线上两个点之间的 **割线（secant line）斜率**。

---

## 2. 从平均变化率逼近瞬时变化率

问题在于：我们真正想知道的是 **点 $x$ 本身** 的变化率。但一个孤立的点没有“距离”，所以不能直接计算变化量除以距离。

于是我们保留第二个点，让它不断靠近第一个点，即 $\Delta x\to0$。此时割线逐渐逼近曲线在 $x$ 处的 **切线（tangent line）**。

所以导数自然定义为：

$$
f'(x)=\lim_{\Delta x\to0}\frac{f(x+\Delta x)-f(x)}{\Delta x}
$$

> **Derivative = instantaneous rate of change = tangent slope.**

---

## 3. 为什么不能直接令 Δx = 0？

因为直接代入会得到 $0/0$，这是没有定义的。

极限做的事情不是“令 $\Delta x=0$”，而是研究：

> **当 $\Delta x$ 可以任意接近 0、但仍不等于 0 时，这个比值趋近于什么？**

这也是导数里最关键的数学动作。

---

## 4. 推一遍最简单的例子：x²

设 $f(x)=x^2$，从差商开始：

$$
\frac{f(x+\Delta x)-f(x)}{\Delta x}
=\frac{(x+\Delta x)^2-x^2}{\Delta x}
$$

展开：

$$
=\frac{x^2+2x\Delta x+(\Delta x)^2-x^2}{\Delta x}
=\frac{2x\Delta x+(\Delta x)^2}{\Delta x}
$$

因为此时 $\Delta x\neq0$，可以约掉一个 $\Delta x$：

$$
=2x+\Delta x
$$

最后才让 $\Delta x\to0$，得到：

$$
\boxed{f'(x)=2x}
$$

所以 $x^2$ 在任意位置 $x$ 的瞬时斜率就是 $2x$。例如 $x=3$ 时，$f'(3)=6$，意味着 $y=x^2$ 在 $(3,9)$ 处切线的斜率为 6。

---

## 5. Mental model

```text
两个点
  ↓
Δy / Δx
  ↓
割线的斜率
  ↓  让 Δx → 0
切线的斜率
  ↓
瞬时变化率
  ↓
导数 f'(x)
```

所以不要把导数定义记成一个需要背诵的公式。它真正表达的是：

> **先用一小段区间测量平均变化，再把这段区间无限缩小，从而得到某一点的瞬时变化。**

## My Understanding

- 导数的起点是 **平均变化率**，不是求导规则。
- 极限把“两点之间”的信息变成“一个点上”的局部信息。
- $\Delta x\to0$ 不等于把 $\Delta x$ 直接设为 0。
- 几何上导数是 **切线斜率**；动态上导数是 **瞬时变化率**。
