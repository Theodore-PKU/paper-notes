# Variational Inference with Normalizing Flows

[TOC]

### 信息

Authors: Danilo Jimenez Rezende, Shakir Mohamed

Google DeepMind

ICML 2015

```latex
@article{rezende2015variational,
  title={Variational inference with normalizing flows},
  author={Rezende, Danilo Jimenez and Mohamed, Shakir},
  journal={arXiv preprint arXiv:1505.05770},
  year={2015}
}
```

这篇文章引用数非常多。1000+

[Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/variational-inference-with-normalizing-flows.pdf)



### 概括

这篇文章提出了一个统一的构建后验分布的框架：normalizing flows。作者将该方法用于 variance inference。更准确地说，normalizing flows 在 inference 中主要起的作用将初始的隐变量变得更加复杂。

normalizing flows 简单地说，就是通过一系列可逆变换，将一个简单的分布变换到一个更复杂的分布。它既可以是有限的变换，也可以是无限的变换（时间 t）。

作者提出的 normlizing flows 是一种较为统一、一般的框架。 此前其他人提出的方法可以看作是 normalizing flows 的特例。



### 理论推导

这篇文章写得比较复杂，不太好懂。只能按照自己的理解来进行理论上的复述。

#### 问题背景

一个借助隐变量的概率模型：
$$
\begin{array}{l}
\log p_{\theta}(\mathbf{x})=\log \int p_{\theta}(\mathbf{x} \mid \mathbf{z}) p(\mathbf{z}) d \mathbf{z} \\
=\log \int \frac{q_{\phi}(\mathbf{z} \mid \mathbf{x})}{q_{\phi}(\mathbf{z} \mid \mathbf{x})} p_{\theta}(\mathbf{x} \mid \mathbf{z}) p(\mathbf{z}) d \mathbf{z} \\
\geq-\mathbb{D}_{\mathrm{KL}}\left[q_{\phi}(\mathbf{z} \mid \mathbf{x}) \| p(\mathbf{z})\right]+\mathbb{E}_{q}\left[\log p_{\theta}(\mathbf{x} \mid \mathbf{z})\right]=-\mathcal{F}(\mathbf{x})
\end{array}
$$
这个式子是所有方法的核心。$\mathcal{F}$ 被称为 free energy bound。在这个式子中，要解决的问题有两个：

1. $\nabla_{\phi}\mathbb{E}_{q}\left[\log p_{\theta}(\mathbf{x} \mid \mathbf{z})\right]$ 的计算。
2. $q(\cdot)$ 的构建。

第一个问题，通过重参数和蒙特卡洛方法可以解决。即对 $\mathbf{z}$ 进行重参数（比如高斯分布，可以用标准正态分布和一个线性变换得到），然后用对 $\mathbf{z}$ 的多次采样来计算期望值：
$$
\nabla_{\phi} \mathbb{E}_{q_{\phi}(z)}\left[f_{\theta}(z)\right] \Leftrightarrow \mathbb{E}_{\mathcal{N}(\epsilon \mid 0,1)}\left[\nabla_{\phi} f_{\theta}(\mu+\sigma \epsilon)\right]
$$
第二个问题，则涉及到 inference network。最简单的做法就是对角高斯概率模型。$q_{\phi}(\mathbf{z} \mid \mathbf{x})=\mathcal{N}\left(\mathbf{z} \mid \boldsymbol{\mu}_{\phi}(\mathbf{x}), \operatorname{diag}\left(\boldsymbol{\sigma}_{\phi}^{2}(\mathbf{x})\right)\right)$，均值和方差都用神经网络实现。

另一方面，关于 $p_{\theta}(\mathbf{x}\mid\mathbf{z})$，也就是 generative model，常常考虑使用 Deep Latent Gaussian Models (DLGM). inference network 和 generative model 就构成了 VAE。DLGM 模型可以写成：
$$
p\left(\mathbf{x}, \mathbf{z}_{1}, \ldots, \mathbf{z}_{L}\right)=p\left(\mathbf{x} \mid f_{0}\left(\mathbf{z}_{1}\right)\right) \prod_{l=1}^{L} p\left(\mathbf{z}_{l} \mid f_{l}\left(\mathbf{z}_{l+1}\right)\right)
$$
这个模型就类似于层级隐变量模型，不过这篇文章没有讲更多的细节。

综上，就完成了 inference 模型的构建。而这篇文章的关注点在于 inference network，如何构建更复杂的隐变量模型。

#### normalizing flows

根本原理是概率论中的分布变量替换。假设 $g\circ f(\mathbf{z}) = \mathbf{z}, \mathbf{z}^\prime = f(\mathbf{z})$，那么：
$$
q\left(\mathbf{z}^{\prime}\right)=q(\mathbf{z})\left|\operatorname{det} \frac{\partial f^{-1}}{\partial \mathbf{z}^{\prime}}\right|=q(\mathbf{z})\left|\operatorname{det} \frac{\partial f}{\partial \mathbf{z}}\right|^{-1}
$$
假设有一系列可逆的变换，$\mathbf{z}_{K}=f_{K} \circ \ldots \circ f_{2} \circ f_{1}\left(\mathbf{z}_{0}\right)$，那么就有：
$$
\ln q_{K}\left(\mathbf{z}_{K}\right)=\ln q_{0}\left(\mathbf{z}_{0}\right)-\sum_{k=1}^{K} \ln \left|\operatorname{det} \frac{\partial f_{k}}{\partial \mathbf{z}_{k-1}}\right|
$$
在这里，$f_k$ 的选择是很重要的，因为这涉及到行列式的计算。复合变换的好处是，对于 $\mathbf{z}_K$ 的计算，往往可以用 $\mathbf{z}_0$ 和函数 $f$ 来实现：
$$
\mathbb{E}_{q_{K}}[h(\mathbf{z})]=\mathbb{E}_{q_{0}}\left[h\left(f_{K} \circ f_{K-1} \circ \ldots \circ f_{1}\left(\mathbf{z}_{0}\right)\right)\right]
$$
只要 $h$ 和 $q_K$ 无关即可。通过 $f_k$，就可以实现将简单的分布变换到复杂的分布。

上述考虑的是有限 flow 的情形，还可以推广到 infinitesimal flows 的情形。论文中提到了两种 flow，Langevin FLow 和 Hamiltonian Flow。无限的 flows 就涉及到时间 t。

#### $f_k$ 的选择

为了计算简化，如何选择函数形式就很重要。这篇文章考虑了两种计算量较低的变换：

1. planar flows $f(\mathbf{z})=\mathbf{z}+\mathbf{u} h\left(\mathbf{w}^{\top} \mathbf{z}+b\right)$
2. radial flows $f(\mathbf{z})=\mathbf{z}+\beta h(\alpha, r)\left(\mathbf{z}-\mathbf{z}_{0}\right), r=\left|\mathbf{z}-\mathbf{z}_{0}\right|, h(\alpha, r)=1 /(\alpha+r)$

这两种 flow 的 Jacobian 矩阵行列式都很容易计算。分别的效果是：planar 对垂直于超平面的方向进行概率上的变化，radial 以某个点为中心进行缩放。

在附录中给出了这两个 flows 满足可逆性的一些条件。

下图是一个 normalizing flow 的例子，将二维高斯分布变成一些更复杂的分布。

![屏幕快照 2020-12-10 下午4.06.37](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-10 下午4.06.37.png)

#### 整体模型

有了 normalizing flows 和 $f_k$ 的选择，怎么构建从 $\mathbf{x}$ 到 $\mathbf{z}$ 呢？

首先作者用一个神经网络将 $\mathbf{x}$ 映射到一个高斯分布，从这个分布中抽样出 $\mathbf{z}_0$，然后用 normalizing flows 得到 $\mathbf{z}_K$，进而用一个网络得到生成的 $\mathbf{x}$

![屏幕快照 2020-12-10 下午8.11.24](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-10 下午8.11.24.png)

带入 free energy bound，由于 $\mathbf{z}$ 关于 $\mathbf{x}$ 的分布就是最终的 $\mathbf{z}_K$，而且随机性由 $\mathbf{z}_0$ 决定。最后就可以推导出下式：
$$
\begin{aligned}
\mathcal{F}(\mathbf{x}) &=\mathbb{E}_{q_{\phi}(z \mid x)}\left[\log q_{\phi}(\mathbf{z} \mid \mathbf{x})-\log p(\mathbf{x}, \mathbf{z})\right] \\
&=\mathbb{E}_{q_{0}\left(z_{0}\right)}\left[\ln q_{K}\left(\mathbf{z}_{K}\right)-\log p\left(\mathbf{x}, \mathbf{z}_{K}\right)\right] \\
&=\mathbb{E}_{q_{0}\left(z_{0}\right)}\left[\ln q_{0}\left(\mathbf{z}_{0}\right)\right]-\mathbb{E}_{q_{0}\left(z_{0}\right)}\left[\log p\left(\mathbf{x}, \mathbf{z}_{K}\right)\right] \\
&-\mathbb{E}_{q_{0}\left(z_{0}\right)}\left[\sum_{k=1}^{K} \ln \mid 1+\mathbf{u}_{k}^{\top} \psi_{k}\left(\mathbf{z}_{k-1}\right)\right]
\end{aligned}
$$
<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-10 下午8.21.11.png" alt="屏幕快照 2020-12-10 下午8.21.11" style="zoom:50%;" />

#### 其他类似的方法

**NICE**

这个方法是一个有限的 volume-preserving flow，这里的 volume-preserving 指的是 Jacobian determinant 始终为 1

flow 的形式是：
$$
\begin{aligned}
f(\mathbf{z}) &=\left(\mathbf{z}_{A}, \mathbf{z}_{B}+h_{\lambda}\left(\mathbf{z}_{A}\right)\right) \\
g\left(\mathbf{z}^{\prime}\right) &=\left(\mathbf{z}_{A}^{\prime}, \mathbf{z}_{B}^{\prime}-h_{\lambda}\left(\mathbf{z}_{A}^{\prime}\right)\right)
\end{aligned}
$$
即将 $\mathbf{z}$ 拆分成两个部分，$h_{\lambda}$ 则是神经网络。拆分的方式也有不同的选择。此处略。这种做法，很容易验证 $f(\mathbf{z})$ 对于 $\mathbf{z}$ 的 Jacobian 矩阵确实是上三角矩阵，而且行列式计算为 1。

文章中还提到了一种 infinitesimal volume-preserving flow, Hamiltonian variational approximation (HVI).



### 实验

作者做了两个实验，一个实验说明 normalizing flows 对于分布变换的能力，另一个实验是用 MNIST，cifar-10 数据集来实现 variance inference，但是这里不太清楚具体的细节。

第一个实验则具有一定的启发性。作者设置了四种不同的非高斯二维分布，然后用 normalizing flows 进行估计（采用 planar flow）。比较的对象是 NICE，另一种 normalizing flows 的特例。

![屏幕快照 2020-12-10 下午3.35.31](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-10 下午3.35.31.png)

(a) 是真实分布，(b) (c) 则是两种方法得到的估计。