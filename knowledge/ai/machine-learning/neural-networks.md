# Neural Networks · 神经网络

神经网络可以先理解为：由许多带可学习参数的计算连接起来的函数。

## 一个神经元

输入 \(x_1,\ldots,x_n\)，权重 \(w_1,\ldots,w_n\)，偏置 \(b\)：

\[
z=w_1x_1+\cdots+w_nx_n+b
\]

经过 activation function（激活函数）：

\[
a=\sigma(z)
\]

因此：

\[
\boxed{\text{输入}\rightarrow\text{加权求和}\rightarrow\text{非线性变换}\rightarrow\text{输出}}
\]

权重决定各输入的影响程度；偏置允许整体平移；激活函数引入非线性。

## 最小网络

\[
x\xrightarrow{w_1,b_1}z_1=w_1x+b_1
\xrightarrow{\sigma}h=\sigma(z_1)
\xrightarrow{w_2,b_2}\hat y=w_2h+b_2
\]

从输入一路计算到预测 \(\hat y\) 叫 **forward pass（前向传播）**。

给定真实答案 \(y\)，用 loss function 衡量预测误差，例如：

\[
L=\frac12(\hat y-y)^2
\]

训练就是调整参数，让 \(L\) 尽可能小。导数

\[
\frac{\partial L}{\partial w}
\]

描述参数改变一点时 loss 如何变化。最基本的更新形式是：

\[
w\leftarrow w-\eta\frac{\partial L}{\partial w}
\]

其中 \(\eta\) 是 learning rate（学习率）。

所以训练的主链路是：

\[
\boxed{\text{Forward}\rightarrow\text{Loss}\rightarrow\text{Gradient}\rightarrow\text{Update}}
\]

网络前面的参数如何高效得到自己的梯度，就是 [Backpropagation](backpropagation.md) 要解决的问题。
