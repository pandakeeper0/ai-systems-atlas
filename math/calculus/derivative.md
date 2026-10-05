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

## 4. 从定义推出多项式的导数

真正值得掌握的不是记住 $x^2$ 的答案，而是看到 **power rule（幂函数求导法则）本身如何从导数定义中长出来**。

先取一个一般的单项式：

$$
f(x)=x^n,\qquad n\in\mathbb{N}
$$

从导数定义出发：

$$
f'(x)
=
\lim_{h\to0}
\frac{f(x+\\Delta x)-f(x)}{\\Delta x}
=
\lim_{h\to0}
\frac{(x+\\Delta x)^n-x^n}{\\Delta x}
$$

利用二项式定理展开：

$$
(x+\\Delta x)^n
=
x^n
+
nx^{n-1}\\Delta x
+
\binom{n}{2}x^{n-2}(\\Delta x)^2
+
\cdots
+
(\\Delta x)^n
$$

代回差商：

$$
\frac{(x+\\Delta x)^n-x^n}{\\Delta x}
=
\frac{
nx^{n-1}\\Delta x
+
\binom{n}{2}x^{n-2}(\\Delta x)^2
+
\cdots
+
(\\Delta x)^n
}{\\Delta x}
$$

因为求极限的过程中 $\\Delta x$ 是**趋近于 0 而不是等于 0**，可以先约掉一个 $\\Delta x$：

$$
=
nx^{n-1}
+
\binom{n}{2}x^{n-2}\\Delta x
+
\cdots
+
(\\Delta x)^{n-1}
$$

现在才令 $h\to0$。所有仍然含有 $\\Delta x$ 的项都会趋近于 0，只剩：

$$
\boxed{
\frac{d}{dx}x^n=nx^{n-1}
}
$$

所以我们平时背的幂函数求导公式并不是额外规定出来的规则，而是 **导数定义 + 二项式展开 + 极限** 的直接结果。

### 从单项式推广到一般多项式

设：

$$
P(x)=a_nx^n+a_{n-1}x^{n-1}+\cdots+a_1x+a_0
$$

从定义：

$$
P'(x)
=
\lim_{h\to0}
\frac{P(x+h)-P(x)}{\\Delta x}
$$

把每一项展开，可以利用极限的线性性质把它拆成各单项式的导数：

$$
P'(x)
=
a_n(nx^{n-1})
+
a_{n-1}((n-1)x^{n-2})
+
\cdots
+
a_1
$$

常数项 $a_0$ 消失，因为：

$$
\frac{a_0-a_0}{\\Delta x}=0
$$

因此：

$$
\boxed{
P'(x)
=
na_nx^{n-1}
+
(n-1)a_{n-1}x^{n-2}
+
\cdots
+
a_1
}
$$

### 一个具体例子

例如：

$$
P(x)=3x^3-2x^2+5x-7
$$

直接由上面的推导得到：

$$
P'(x)=9x^2-4x+5
$$

这里每一个“指数掉下来、指数减一”的动作，都可以追溯回 $(x+\\Delta x)^n$ 的二项式展开：当 $h\to0$ 时，只有 **恰好含一个 $\\Delta x$ 的一阶项** 能在除以 $\\Delta x$ 后留下来。

> **这就是 power rule 最值得记住的来源：差商除掉一个 $\\Delta x$ 后，高阶 $\\Delta x$ 项在极限中全部消失，只留下 $nx^{n-1}$。**

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
