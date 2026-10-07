# Backpropagation · 反向传播

> 反向传播不是一种神秘的“神经网络专属数学”。它的核心就是：在一连串复合计算上，高效应用链式法则。

先沿用 [Neural Networks](neural-networks.md) 中的最小网络：

\[
x\rightarrow z_1\rightarrow h\rightarrow\hat y\rightarrow L
\]

其中：

\[
z_1=w_1x+b_1,\qquad h=\sigma(z_1),\qquad \hat y=w_2h+b_2
\]

并取：

\[
L=\frac12(\hat y-y)^2
\]

## 1. 真正的问题

我们想训练最前面的参数 \(w_1\)。

但 \(w_1\) 并不直接决定 loss：

\[
w_1\rightarrow z_1\rightarrow h\rightarrow\hat y\rightarrow L
\]

所以问题是：

> \(w_1\) 改变一点，最终的 \(L\) 会改变多少？

也就是求：

\[
\frac{\partial L}{\partial w_1}
\]

## 2. 用链式法则把影响一层层传回来

因为影响沿着

\[
w_1\rightarrow z_1\rightarrow h\rightarrow\hat y\rightarrow L
\]

传播，所以链式法则给出：

\[
\boxed{
\frac{\partial L}{\partial w_1}
=
\frac{\partial L}{\partial\hat y}
\frac{\partial\hat y}{\partial h}
\frac{\partial h}{\partial z_1}
\frac{\partial z_1}{\partial w_1}
}
\]

逐项计算：

\[
\frac{\partial L}{\partial\hat y}=\hat y-y
\]

\[
\frac{\partial\hat y}{\partial h}=w_2
\]

\[
\frac{\partial h}{\partial z_1}=\sigma'(z_1)
\]

\[
\frac{\partial z_1}{\partial w_1}=x
\]

因此：

\[
\boxed{
\frac{\partial L}{\partial w_1}
=(\hat y-y)w_2\sigma'(z_1)x
}
\]

这就是最小版本的 backpropagation。

## 3. 为什么叫“反向传播”？

前向计算：

\[
x\rightarrow z_1\rightarrow h\rightarrow\hat y\rightarrow L
\]

求梯度时则从最终 loss 往回：

\[
L\rightarrow\hat y\rightarrow h\rightarrow z_1\rightarrow w_1
\]

逐层计算“loss 对这里有多敏感”。

所以：

\[
\boxed{\text{Forward：算出预测和 loss}}
\]

\[
\boxed{\text{Backward：从 loss 出发，利用链式法则算出参数梯度}}
\]

## 4. 为什么不直接分别求每个参数？

大型网络可能有数十亿参数。许多参数到 loss 的路径共享大量中间计算。

Backpropagation 的关键工程价值是：**从后往前复用已经算出的局部梯度**，而不是为每个参数独立重复整条求导过程。

现代框架里的 automatic differentiation（自动微分）和 computation graph（计算图）会把这个过程系统化；后续再单独展开。

## Mental model

\[
\boxed{
\text{Backpropagation}
=
\text{Chain Rule 在计算图上的高效复用}
}
\]

一次训练可以看成：

\[
\boxed{
\text{Forward}
\rightarrow
\text{Loss}
\rightarrow
\text{Backprop}
\rightarrow
\nabla L
\rightarrow
\text{Optimizer Update}
}
\]

注意区分：

- **Backpropagation**：计算梯度；
- **Optimizer**：拿到梯度以后决定怎样修改参数；
- **Training**：把前向、loss、反向传播和参数更新组合起来并反复执行。
