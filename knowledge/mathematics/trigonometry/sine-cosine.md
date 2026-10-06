# Sine & Cosine · 正弦与余弦

> 在学习 Fourier Series / Fourier Transform 之前，先从最基本的几何对象出发，理解正弦波为什么是这样的波形，以及周期、频率、角频率、振幅和相位分别意味着什么。

## 1. 从单位圆开始

先不把 \(\sin\) 和 \(\cos\) 当作两条需要记忆的曲线。

考虑半径为 1 的圆，也就是 **unit circle（单位圆）**。一个点 \(P\) 从 \((1,0)\) 出发，沿圆周逆时针运动。

为了描述它转了多少，引入角度 \(\theta\)。这里使用 **radian（弧度）**：

\[
\theta=\frac{\text{走过的圆弧长度}}{\text{半径}}
\]

单位圆的半径为 1，因此：

\[
\theta=\text{走过的圆弧长度}
\]

单位圆的周长是 \(2\pi\)，所以完整转一圈对应：

\[
\theta=2\pi
\]

这就是后面正弦、余弦的周期为什么天然与 \(2\pi\) 联系在一起。

## 2. Sine 和 Cosine 是圆周运动的两个坐标

当点转到角度 \(\theta\) 时，设它的位置为：

\[
P=(x,y)
\]

定义：

\[
x=\cos\theta,\qquad y=\sin\theta
\]

因此可以先形成一个非常直接的几何理解：

\[
\boxed{\cos\theta=\text{圆周运动的横坐标}}
\]

\[
\boxed{\sin\theta=\text{圆周运动的纵坐标}}
\]

几个关键位置：

| \(\theta\) | 点 \(P\) | \(\cos\theta\) | \(\sin\theta\) |
| --- | --- | --- | --- |
| \(0\) | \((1,0)\) | 1 | 0 |
| \(\pi/2\) | \((0,1)\) | 0 | 1 |
| \(\pi\) | \((-1,0)\) | -1 | 0 |
| \(3\pi/2\) | \((0,-1)\) | 0 | -1 |
| \(2\pi\) | \((1,0)\) | 1 | 0 |

## 3. 为什么它会形成“波”？

只观察圆周运动点的纵坐标。

随着 \(\theta\) 从 \(0\) 增加到 \(2\pi\)，纵坐标经历：

\[
0\rightarrow1\rightarrow0\rightarrow-1\rightarrow0
\]

如果横轴记录角度 \(\theta\)，纵轴记录这个点的纵坐标，就得到：

\[
y=\sin\theta
\]

因此正弦波不是凭空规定出来的一条曲线。一个非常有用的心智模型是：

\[
\boxed{\text{正弦波 = 匀速圆周运动在一个方向上的投影}}
\]

余弦同理，只是观察横坐标。

## 4. 周期为什么是 \(2\pi\)？

点完整转一圈后回到原来的位置，而完整一圈的角度是 \(2\pi\)。

因此：

\[
\sin(\theta+2\pi)=\sin\theta
\]

\[
\cos(\theta+2\pi)=\cos\theta
\]

**Period（周期）**表示输入增加多少后，函数完整重复一次。

所以 \(\sin\theta\) 和 \(\cos\theta\) 的基本周期都是：

\[
\boxed{T_\theta=2\pi}
\]

这里先写成 \(T_\theta\)，强调这是“角度轴”上的周期。接下来把时间引入后，才会得到以秒计的时间周期。

## 5. 从角度变成随时间变化的波

假设圆上的点不是任意运动，而是**匀速转动**。

如果它每秒转过 \(\omega\) 弧度，那么经过 \(t\) 秒：

\[
\boxed{\theta=\omega t}
\]

\(\omega\) 称为 **angular frequency（角频率）**，常用单位是 rad/s。

把它代入 \(\sin\theta\)：

\[
\boxed{y(t)=\sin(\omega t)}
\]

所以 \(\omega\) 的来源很具体：它就是圆周运动每秒扫过多少弧度。

\(\omega\) 越大，同样时间内点绕圆走得越远，因此波振荡得越快。

## 6. 时间周期、频率和角频率

一个完整周期意味着点正好转了一整圈：

\[
\omega T=2\pi
\]

因此时间周期：

\[
\boxed{T=\frac{2\pi}{\omega}}
\]

它的含义就是：

\[
\text{转一圈需要的时间}
=
\frac{\text{一圈的角度}}{\text{每秒转过的角度}}
\]

普通的 **frequency（频率）** \(f\) 问的是“每秒完成多少个周期”，单位是 Hz：

\[
\boxed{f=\frac1T}
\]

一圈对应 \(2\pi\) rad，所以：

\[
\boxed{\omega=2\pi f=\frac{2\pi}{T}}
\]

因此 \(T\)、\(f\)、\(\omega\) 描述的是同一种重复速度，只是观察方式和单位不同。

## 7. 振幅从哪里来？

单位圆产生的 \(\sin(\omega t)\) 在 \([-1,1]\) 之间变化。

如果把纵向尺度放大 \(A\) 倍：

\[
\boxed{y(t)=A\sin(\omega t)}
\]

其中 \(A\) 是 **amplitude（振幅）**。

它控制的是波有多“高”，而不是振荡有多“快”：

- \(|A|\) 变大：纵向幅度变大；
- \(\omega\) 变大：横向振荡变快。

## 8. 相位为什么会出现？

前面的表达式默认 \(t=0\) 时圆周运动恰好从固定位置开始。

但点完全可以在 \(t=0\) 时已经提前转过一个角度 \(\phi\)。此时：

\[
\boxed{\theta=\omega t+\phi}
\]

因此：

\[
\boxed{y(t)=A\sin(\omega t+\phi)}
\]

\(\phi\) 称为 **phase（相位）**。

它描述的不是转得多快，而是：

> \(t=0\) 时，这个周期运动已经处在一圈中的什么位置。

于是一个最基本的正弦波：

\[
A\sin(\omega t+\phi)
\]

中的三个参数都有明确含义：

| 参数 | 名称 | 含义 |
| --- | --- | --- |
| \(A\) | Amplitude · 振幅 | 波有多高 |
| \(\omega\) | Angular Frequency · 角频率 | 每秒扫过多少弧度 |
| \(\phi\) | Phase · 相位 | \(t=0\) 时位于周期中的哪里 |

## 9. Sine 和 Cosine 其实是同一种波

Sine 看单位圆的纵坐标，cosine 看横坐标。

横坐标与纵坐标的变化相差四分之一圈：

\[
\frac{2\pi}{4}=\frac{\pi}{2}
\]

因此：

\[
\boxed{\cos\theta=\sin\left(\theta+\frac{\pi}{2}\right)}
\]

所以 sine 和 cosine 并不是两种本质不同的周期运动。

\[
\boxed{\text{sine 与 cosine = 同一种周期运动的两个相差 }\pi/2\text{ 的相位}}
\]

## 10. Mental model

\[
\boxed{
\text{圆周运动}
\rightarrow
\text{一个方向上的投影}
\rightarrow
\sin/\cos
\rightarrow
\text{周期波}
}
\]

再让圆周运动随时间匀速进行：

\[
\boxed{
\theta=\omega t+\phi
\rightarrow
y(t)=A\sin(\omega t+\phi)
}
\]

其中：

\[
A\;\text{决定高度},\qquad
\omega\;\text{决定快慢},\qquad
\phi\;\text{决定起点}
\]

## 11. 下一步：从简单周期波走向复杂周期信号

到这里还没有使用 Fourier。

下一步自然的问题是：

> 如果 \(A\)、\(\omega\)、\(\phi\) 可以完整描述一个简单的周期波，那么现实中那些并不像正弦波的复杂周期信号是怎么形成的？

可以从两个简单周期运动的叠加开始：

\[
\sin t+\sin 2t
\]

观察不同频率的简单波叠加后为什么会形成更复杂的波形。之后再讨论如何反过来从复杂信号中识别这些简单频率成分，这才会自然走向 Fourier Series 和 Fourier Transform。
