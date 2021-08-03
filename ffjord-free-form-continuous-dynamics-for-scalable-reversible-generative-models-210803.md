# FFJORD: Free-form Continuous Dynamics for Scalable Reversible Generative Models

[TOC]

## 信息

Authors: Will Grathwohl, Ricky T. Q. Chen, Jesse Bettencourt, Ilya Sutskever, David Duvenaud

Year: ICLR 2019

Bibtex:

```latex
@article{grathwohl2018ffjord,
  title={Ffjord: Free-form continuous dynamics for scalable reversible generative models},
  author={Grathwohl, Will and Chen, Ricky TQ and Bettencourt, Jesse and Sutskever, Ilya and Duvenaud, David},
  journal={arXiv preprint arXiv:1810.01367},
  year={2018}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/ffjord-free-form-continuous-dynamics-for-scalable-reversible-generative-models.pdf)



## 概括

FFJORD 模型是 CNF 模型的拓展，提出了一种高效的计算似然概率的方法，并且打破了网络结构设计的局限性。通常来说，对于 flow 模型，网络结构需要精心设计使得似然函数可以计算（梯度的行列式）。但 FFJORD 模型利用 ODE 模型（也就是 CNF）实现了网络结构无限制的连续 flow 模型。具体来说，这篇文章提出使用 Hutchinson trace estimator 来计算对数概率变换，可以大大减少极大似然估计损失函数的计算量。



## 方法

如果对于 flow 模型中的变换函数无限制，通常情况下 Jacobian 的行列式计算是 $O(D^3)$ 的计算量。这太大了。

对于 CNF 模型而言，设 $\frac{\part z(t)}{\part t} = f(z(t), t;\theta)$，那么对数概率的变化满足：
$$
\frac{\partial \log p(\mathbf{z}(t))}{\partial t}=-\operatorname{Tr}\left(\frac{\partial f}{\partial \mathbf{z}(t)}\right)
$$

$$
\log p\left(\mathbf{z}\left(t_{1}\right)\right)=\log p\left(\mathbf{z}\left(t_{0}\right)\right)-\int_{t_{0}}^{t_{1}} \operatorname{Tr}\left(\frac{\partial f}{\partial \mathbf{z}(t)}\right) d t
$$

和原本的 flow 模型比较：
$$
\log p_{\mathbf{x}}(\mathbf{x})=\log p_{\mathbf{z}}(\mathbf{z})-\log \operatorname{det}\left|\frac{\partial f(\mathbf{z})}{\partial \mathbf{z}}\right|
$$
用 Trace 的积分计算替代了对数概率的变化。

对于 CNF 模型，而言，我们要计算loss 关于 theta 的梯度，我个人感觉是比较复杂的。参见 CNF 的论文和这篇论文的 2.2.1 节。我个人觉得直接对 2 式进行自动微分就可以了。只要能够把 Trace 的积分的离散形式写出来，因为 f 是一个网络，我们需要先用一个 ODE solver 得到相应的 z(t)，然后再用一个离散形式计算积分，这样不就可以直接算出 loss 了吗？我感觉会简单很多。虽然这样做会很简单，但是当离散程度很高的时候，loss 的计算会占用很大的内存。作者在2.2.1 节给出的方法的好处是关于网络参数的梯度也变成一个积分，可以在求解 ODE 的同时进行计算，也就是说这个过程是在 Trace 的估计的基础上直接算出来的。我刚才的想法则是需要再次用到自动微分，这时候实际上是二次微分了（有点像 WGAN 里面的一些计算）。

Unbiased 对数概率估计：Hutchinson trace estimator。

利用 $\operatorname{Tr}(A)=E_{p(\boldsymbol{\epsilon})}\left[\boldsymbol{\epsilon}^{T} A \boldsymbol{\epsilon}\right]$，这里满足 $\mathbb{E}[\boldsymbol{\epsilon}]=0 \text { and } \operatorname{Cov}(\boldsymbol{\epsilon})=I$，那么我们可以将原来的对数概率计算变成：
$$
\begin{aligned}
\log p\left(\mathbf{z}\left(t_{1}\right)\right) &=\log p\left(\mathbf{z}\left(t_{0}\right)\right)-\int_{t_{0}}^{t_{1}} \operatorname{Tr}\left(\frac{\partial f}{\partial \mathbf{z}(t)}\right) d t \\
&=\log p\left(\mathbf{z}\left(t_{0}\right)\right)-\int_{t_{0}}^{t_{1}} \mathbb{E}_{p(\boldsymbol{\epsilon})}\left[\boldsymbol{\epsilon}^{T} \frac{\partial f}{\partial \mathbf{z}(t)} \boldsymbol{\epsilon}\right] d t \\
&=\log p\left(\mathbf{z}\left(t_{0}\right)\right)-\mathbb{E}_{p(\boldsymbol{\epsilon})}\left[\int_{t_{0}}^{t_{1}} \boldsymbol{\epsilon}^{T} \frac{\partial f}{\partial \mathbf{z}(t)} \boldsymbol{\epsilon} d t\right]
\end{aligned}
$$
在这个计算中，其实还用到了一个技巧，那就是给定向量 v, $\boldsymbol{v}^{T} \frac{\partial f}{\partial \mathbf{z}}$ 的计算利用自动微分是非常高效的。因为 $\boldsymbol{v}^{T} \frac{\partial f}{\partial \mathbf{z}} = \frac{\partial( \boldsymbol{v}^{T} f)}{\partial \mathbf{z}}$. 所以只要先算出 v^t f，然后对 z 自动微分求导就可以了，最后再和 v 做内积乘法。这里的 $\epsilon$ 可以是标准高斯分布或者 Rademacher 分布。

但是这个 Trace 估计是存在 variance 的，作者在 3.1.1 节中说，如果网络的中间层的维数比较小，那么可以减小 variance。（但是这就会导致网络的表达能力变弱很多）。

现在在 FFJORD 模型中，计算量变成 $\mathcal{O}((D H+D) \hat{L})$，这里的 H 表示 f 网络中间层的最大值，D 表示信号的维数，L 表示 ODE solver 的离散程度（我个人猜测）

网络结构：我感觉作者的网络 f 是全连接网络。作者在论文中说，他们设计了不同的将 t 作为 f 的输入的方式，最后发现还是直接将 t 和 z(t) 合并作为输入效果好。



## 实验

作者在 toy 实验中，训练的时候用 Hutchinson's trace estimator, 在测试的时候计算真实的 trace。但是在图像数据集上，MNIST 和 CIFAR10，都是用估计，因为直接算太难了。

作者的 toy 实验有一个和我们自己的 toy2d 实验非常像，而且我们的 2d 实验的效果似乎比这里给出的 glow 模型效果好。但是 FFJORD 模型效果好非常多。

和 glow 这样的模型相比，FFJORD 的参数要少很多，但是计算更慢。作则表示，他们也是用多尺度的结构（多个 flow），效果会更好。

作者还将 FFJORD 模型用于 VAE，比较 VAE 是用其他 flow 模型或者不使用 flow 模型的 ELBO 的结果。

在消融实验中，一个很重要的是 ODE 的离散程度（function evaluation number），如果 NFE 越大，估计的效果自然是越好。



## 局限

这篇文章基本上可以说是对 CNF 模型训练的改进，即通过 Trace 估计的方法来实现网络训练，降低训练的计算量和难度。同时也放宽了网络的形式的限制。但是 CNF方法仍然有一些局限。一个是 NFE 的数量，这仍然影响了计算效率。另外就是 ODE solver 本身的问题，能否高效求解 ODE 也是一个局限。



