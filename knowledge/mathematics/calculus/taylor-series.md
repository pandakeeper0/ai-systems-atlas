# Taylor Series · 泰勒级数展开

> **核心问题：** 如果知道函数在某个点的值、斜率和更高阶局部变化信息，能否重建这个点附近的函数？

Taylor 展开的结构可以从**导数定义、积分作为变化率的累积，以及微积分基本定理**一步步推出。

## 1. 从导数得到一阶近似

选择展开点 \(x_0\)，令 \(\Delta x=x-x_0\)。

\[
f'(x_0)=\lim_{\Delta x\to0}\frac{f(x_0+\Delta x)-f(x_0)}{\Delta x}
\]

因此，当 \(\Delta x\) 很小时，

\[
\boxed{f(x_0+\Delta x)\approx f(x_0)+f'(x_0)\Delta x}
\]

定义一阶余项：

\[
R_1(\Delta x)=f(x_0+\Delta x)-f(x_0)-f'(x_0)\Delta x
\]

代回导数定义可得：

\[
\boxed{\lim_{\Delta x\to0}\frac{R_1(\Delta x)}{\Delta x}=0}
\]

也就是 \(R_1\) 比 \(\Delta x\) 缩小得更快，简写为

\[
R_1(\Delta x)=o(\Delta x)
\]

这里的小写 \(o\) 只是上述极限关系的缩写，并不表示余项等于零。

## 2. 对导函数重复同一个过程

把 \(g(x)=f'(x)\) 看成新函数，同样有

\[
\boxed{f'(x_0+t)=f'(x_0)+f''(x_0)t+R(t)}
\]

且

\[
\lim_{t\to0}\frac{R(t)}t=0
\]

这里 \(t\) 表示从 \(x_0\) 出发当前走过的距离；最终总距离是 \(\Delta x\)。

## 3. 积分：把变化率累积回函数变化

\[
\boxed{f(x_0+\Delta x)-f(x_0)=\int_0^{\Delta x}f'(x_0+t)\,dt}
\]

直觉是：

> 变化率 × 一小段距离 = 这一小段里的函数变化；把所有小段累积起来 = 总变化。

定积分可以理解为黎曼和的极限：

\[
\int_a^b g(t)\,dt
=
\lim_{\text{分割越来越细}}\sum_i g(t_i)\Delta t_i
\]

“积分导数得到总变化”就是微积分基本定理在这里的核心作用。

## 4. 二阶项从哪里来

把对 \(f'\) 的一阶展开代入：

\[
f(x_0+\Delta x)-f(x_0)
=
\int_0^{\Delta x}[f'(x_0)+f''(x_0)t+R(t)]dt
\]

前两项分别是

\[
\int_0^{\Delta x}f'(x_0)dt=f'(x_0)\Delta x
\]

和

\[
\int_0^{\Delta x}f''(x_0)t\,dt
=
\frac12f''(x_0)(\Delta x)^2
\]

第二个积分对应的 \(y=f''(x_0)t\) 本身就是直线，所以三角形面积是**精确的**，不是近似。近似发生在用这条直线描述真实的 \(f'(x_0+t)\) 时，二者的差异由余项承担。

因此：

\[
\boxed{
f(x_0+\Delta x)
=
f(x_0)+f'(x_0)\Delta x+
\frac{f''(x_0)}{2!}(\Delta x)^2+
\text{更高阶余项}
}
\]

二阶 Taylor 余项满足

\[
\boxed{R_2(\Delta x)=o((\Delta x)^2)}
\]

即

\[
\lim_{\Delta x\to0}\frac{R_2(\Delta x)}{(\Delta x)^2}=0
\]

## 5. 三阶项与阶乘

继续对 \(f''\) 做同样的一阶展开，再逐层积分回 \(f\)。三阶导数的贡献最终是

\[
\boxed{\frac{f'''(x_0)}{3!}(\Delta x)^3}
\]

阶乘不是人为规定的。因为每积分一次：

\[
\int t^kdt=\frac{t^{k+1}}{k+1}
\]

高阶变化率逐层积分回原函数时，就自然累积出

\[
\frac1{2\cdot3\cdots n}=\frac1{n!}
\]

## 6. 推广到 n 阶

不断重复得到

\[
\boxed{
T_n(x)=
\sum_{k=0}^{n}\frac{f^{(k)}(x_0)}{k!}(x-x_0)^k
}
\]

以及

\[
\boxed{f(x)=T_n(x)+R_n(x)}
\]

每一阶导数都在增加更精细的局部变化信息。因此可以把 Taylor 理解成：

\[
\boxed{
\text{局部函数}
=
\text{值}
+
\text{一阶变化}
+
\text{二阶变化}
+
\text{三阶变化}
+\cdots
}
\]

## 7. 两种完全不同的“余项趋于零”

这是最容易混淆的地方。

**固定阶数，让 \(x\to x_0\)：**

例如二阶展开研究的是

\[
\Delta x\to0,
\qquad
R_2(\Delta x)=o((\Delta x)^2)
\]

它回答：**固定使用二阶 Taylor，离展开点越近时有多准？**

**固定附近的一个 \(x\)，让 \(n\to\infty\)：**

\[
\boxed{\lim_{n\to\infty}R_n(x)}
\]

它回答：**不断增加 Taylor 阶数，最终能否把这个位置上的真实函数完全还原？**

所以：

\[
\boxed{\Delta x\to0\quad\text{和}\quad n\to\infty}
\]

是两个不同的问题。

## 8. 光滑与解析

### 光滑（smooth）

\[
f\in C^\infty
\]

表示函数具有任意阶连续导数。也就是说，在 \(x_0\) 可以取得

\[
f(x_0),f'(x_0),f''(x_0),\ldots
\]

全部局部导数信息。

但：

\[
\boxed{\text{所有阶导数都存在}\not\Rightarrow\text{Taylor 级数等于原函数}}
\]

### 解析（analytic）

解析性更强。它意味着：

> **一个点上的全部阶导数信息，足以在某个邻域内完整重建函数。**

也就是说，在某个 \(|x-x_0|<r\) 的范围内，

\[
\boxed{
f(x)=
\sum_{k=0}^{\infty}
\frac{f^{(k)}(x_0)}{k!}(x-x_0)^k
}
\]

等价地，对邻域内的固定 \(x\)：

\[
\boxed{\lim_{n\to\infty}R_n(x)=0}
\]

可以记成：

> **光滑：无限阶局部信息都存在。**  
> **解析：这些无限阶局部信息没有遗漏附近函数的任何信息。**

## 9. 光滑但不解析的经典反例

考虑

\[
f(x)=
\begin{cases}
e^{-1/x^2}, & x\neq0\\
0, & x=0
\end{cases}
\]

它在 \(x_0=0\) 无限阶可导，而且

\[
\boxed{f^{(n)}(0)=0\quad\text{对所有 }n}
\]

所以无论取多少阶：

\[
T_n(x)=0
\]

但任意固定的 \(x\neq0\) 都有

\[
f(x)=e^{-1/x^2}>0
\]

于是

\[
R_n(x)=f(x)-T_n(x)=e^{-1/x^2}
\]

根本不会因为 \(n\) 增大而改变。例如固定 \(x=0.1\)：

\[
\lim_{n\to\infty}R_n(0.1)=e^{-100}\neq0
\]

因此它在 0 **无限光滑，却不解析**。

这个例子说明：

\[
\boxed{
\text{知道一个点的所有阶导数，仍然未必知道附近的函数}
}
\]

解析函数则具有更强的“局部刚性”：这些无限阶局部信息足以确定附近的函数。

## 10. 展开点为什么重要

Taylor 描述的是函数在展开中心附近的局部行为。

令

\[
\Delta x=x-x_0
\]

则

\[
1,\Delta x,(\Delta x)^2,(\Delta x)^3,\ldots
\]

表示距离 \(x_0\) 的不同阶局部尺度。\(|\Delta x|\) 足够小时，高阶幂越来越小，因此高阶项通常是越来越精细的局部修正。

当 \(x_0=0\) 时，Taylor 展开称为 **Maclaurin Series（麦克劳林级数）**。

## 11. 最终心智模型

\[
\boxed{
\text{导数定义}
\rightarrow
\text{局部一阶变化}
\rightarrow
\text{对导数继续求导}
\rightarrow
\text{积分累积变化}
\rightarrow
\text{高阶修正}
\rightarrow
\text{Taylor 多项式}
}
\]

然后还要独立检查：

\[
\boxed{R_n(x)\xrightarrow[n\to\infty]{}0;?}
\]

如果成立，Taylor 多项式的极限才能真正还原原函数。

> **一句话：Taylor 展开是在一个点提取函数的各阶局部变化信息，再通过微积分把这些信息逐层累积回来；解析性则保证这套无限阶局部信息最终没有遗漏任何函数信息。**
