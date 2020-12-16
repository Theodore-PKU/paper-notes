# Improved variational inference with inverse autoregressive flow

[TOC]

### 信息

Authors:

NeurIPS 2016

```latex
@inproceedings{kingma2016improved,
  title={Improved variational inference with inverse autoregressive flow},
  author={Kingma, Durk P and Salimans, Tim and Jozefowicz, Rafal and Chen, Xi and Sutskever, Ilya and Welling, Max},
  booktitle={Advances in neural information processing systems},
  pages={4743--4751},
  year={2016}
}
```

引用：900+

[Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/improved-variational-inference-with-inverse-autoregressive-flow.pdf)



### 概括

这篇文章使用的 variance inference 框架仍然是 VAE，对于 VAE 的描述不是很多，因为论文的关键贡献在于如何将 normalizing flow 应用于 variance inference。基本做法和 normalizing flow 那篇文章一样，只不过这篇文章使用的分布变换，是通过对自回归模型的分析得到的一个类似自回归形式的分布变换函数。因此自回归模型就成为 normalizing flow 模型的 one step。不过和自回归模型不同的一个细节在于输出的分量并没有参与到下一个输出分量的计算（所以和真正的自回归不一样，这一点，Masked Autoregressive Flow for Density Estimation 才用到。所以 inference 的时候，计算是高效的（比自回归快）。所以也可以理解为这篇文章就是使用了另一种形式的分布变换函数，使得 Jacobian 矩阵仍然保持一个很好计算的性质，但整体框架仍然是 VAE 模型。

虽然没有详细说 VAE 模型的结构，但是从论文给出的图示来看，和 NVAE 模型已经非常接近了，IAF 也是用于 $\mathbf{z}|\mathbf{x}$ 的后续。

我对这篇文章最大的疑惑在于如果用 IAF，那么隐变量就不应该是高斯分布，而 $\mathbf{z}$ 的先验分布呢？如果不知道 IAF 变换之后的分布是什么，还使用原来的高斯分布作为先验，那么这不就相当于将一个高斯分布变成另一个分布，然后希望这个分布和高斯分布接近？那么这个分布变换有什么用呢？是不是也可以理解成直接增加条件分布（inference model）的表达能力？



### Code

https://github.com/openai/iaf

不过可能是比较老的框架写的。



### 方法

这篇文章的方法综合了 VAE（我个人感觉这 VAE 更准确地说应该是 Variance inference）、自回归（auto-regressive）和 normalizing flow（这里不使用 flow-based 的说法是因为单纯使用了这个形式，但是并不是作为一个生成模型）。

#### variational inference 背景

这个部分和 VAE 的推导几乎是一样的，但是推导的思路不同。不知道是不是原始 VAE 论文里的写法，毕竟作者就是 VAE 的作者。

首先，目标是极大化似然函数 $\log p(\mathbf{X})=\sum_{i=1}^{N} \log p\left(\mathbf{x}^{(i)}\right)$，这个毫无争议。如何计算每个 datapoint 的概率呢？引入隐变量，这里的隐变量由参数化的 inference model 得到。并且，利用詹森不等式，可以变成一个下界：
$$
\log p(\mathbf{x}) \geq \mathbb{E}_{q(\mathbf{z} \mid \mathbf{x})}[\log p(\mathbf{x}, \mathbf{z})-\log q(\mathbf{z} \mid \mathbf{x})]=\mathcal{L}(\mathbf{x} ; \boldsymbol{\theta})
$$
在这里 $q$ 模型是 inference model，而 $p$ 模型可能和 decoder 有关。$\mathcal{L}(\mathbf{x}; \theta)$ 和之前自己推导的 VAE 模型的目标函数是一致的。所以这也可以看成是 VAE 模型的一个推导。很显然，这个写法和 flow-based model 不一样。flow-based model 计算的损失函数不是下界。

另一方面，$\mathcal{L}(\mathbf{x}; \theta)$ 也可以写成
$$
\mathcal{L}(\mathbf{x} ; \boldsymbol{\theta})=\log p(\mathbf{x})-D_{K L}(q(\mathbf{z} \mid \mathbf{x}) \| p(\mathbf{z} \mid \mathbf{x}))
$$
所以极大化 $\mathcal{L}$ 就是要减小 $D_{KL}$，这个结果和之前的 VAE 推导是类似的。在这里 $p(\mathbf{z}|\mathbf{x})$ 是真实的分布，仅此我们要让 $q$ 模型尽可能地接近真实分布。

> 这里我有一个疑问，真实分布 $p(\mathbf{z}|\mathbf{x})$ 是什么样呢？我们其实事先也不知道。

但不管怎么样，这篇文章的主要目的是利用自回归模型和 normalizing flow 的方法，使得 $q$ 模型的表达能力更强。

最后，在这个部分，作者还提到了multiple latent variables 的情况。也就是隐变量有一定的顺序（或者说层级），比如两个隐变量：$\text { e.g. } q\left(\mathbf{z}_{a}, \mathbf{z}_{b} \mid \mathbf{x}\right)=q\left(\mathbf{z}_{a} \mid \mathbf{x}\right) q\left(\mathbf{z}_{b} \mid \mathbf{z}_{a}, \mathbf{x}\right)$，往往会写成这种形式。

#### 目标

让 $q(\mathbf{z}|\mathbf{x})$ 计算高效，且灵活，足以表达 $p(\mathbf{z}|\mathbf{x})$

高效一般采用高斯分布（对角，每个分量独立）
$$
q(\mathbf{z} \mid \mathbf{x}) \sim \mathcal{N}\left(\boldsymbol{\mu}(\mathbf{x}), \boldsymbol{\sigma}^{2}(\mathbf{x})\right)
$$
均值和方差通过一个非线性函数（神经网络）得到。

#### Normalizing flow

normalizing flow 在提出的时候，其目的就是为了增强 $q$ 模型的表达能力，这才是真正的核心，并不是一个 flow-based 模型。其做法是先得到一个隐变量 $\mathbf{z}_0$，通过一个 $q$ 模型，然后再利用可逆的参数化变换 $\mathbf{f}_t$ jiang $\mathbf{z}_0$ 变得更复杂。
$$
\mathbf{z}_{0} \sim q\left(\mathbf{z}_{0} \mid \mathbf{x}\right), \quad \mathbf{z}_{t}=\mathbf{f}_{t}\left(\mathbf{z}_{t-1}, \mathbf{x}\right) \quad \forall t=1 \ldots T
$$
在这种情况下，原来的损失函数中的 $\log q(\mathbf{z}|\mathbf{x})$ 就变成更为复杂的：
$$
\log q\left(\mathbf{z}_{T} \mid \mathbf{x}\right)=\log q\left(\mathbf{z}_{0} \mid \mathbf{x}\right)-\sum_{t=1}^{T} \log \operatorname{det}\left|\frac{d \mathbf{z}_{t}}{d \mathbf{z}_{t-1}}\right|
$$
原来的 normalizing flow 的参数化形式是
$$
\mathbf{f}_{t}\left(\mathbf{z}_{t-1}\right)=\mathbf{z}_{t-1}+\mathbf{u} h\left(\mathbf{w}^{T} \mathbf{z}_{t-1}+b\right)
$$
作者在文中评论，这种形式相当于一个中间层为 1 个节点的 MLP 网络。

作者认为 planar flows 和 radial flows 对于低维数据是有效的，但是对高维数据，可能不太行。

#### Inverse Autoregressive Transformations

这篇文章的核心就是通过对自回归模型的分析，得到一个新的 normalizing flow 的变换形式。

在一般的自回归模型中，$y_i$ 关于 $y_{1:i-1}$ 是一个高斯分布，也就是说 $y_i$ 的均值和方差由 $y_{i:i-1}$ 决定。通常这种模型在采样的时候，先从一个正态分布 $\mathbf{\epsilon} \sim \mathcal{N}(0, 1)$ 中采样，然后用
$$
y_{0}=\mu_{0}+\sigma_{0} \odot \epsilon_{0}\\
y_{i}=\mu_{i}\left(\mathbf{y}_{1: i-1}\right)+\sigma_{i}\left(\mathbf{y}_{1: i-1}\right) \cdot \epsilon_{i}, i > 0
$$
得到 $y$ 的采样。这个形式也可以写成 $\epsilon_{i}=\frac{y_{i}-\mu_{i}\left(\mathbf{y}_{1: i-1}\right)}{\sigma_{i}\left(\mathbf{y}_{1: i-1}\right)}$

类似于 flow-based model 的分析，可以把 $\epsilon$ 和 $\mathbf{y}$ 看成变量之间的一个变换关系，而且 Jacobian 矩阵是三角矩阵，行列式很好计算：
$$
\begin{equation}
\log \operatorname{det}\left|\frac{d \boldsymbol{\epsilon}}{d \mathbf{y}}\right|=\sum_{i=1}^{D}-\log \sigma_{i}(\mathbf{y})
\end{equation}
$$
利用这个形式，假设初始的隐变量是通过下式采样得到：
$$
\begin{equation}
\mathbf{z}_{0}=\boldsymbol{\mu}_{0}+\boldsymbol{\sigma}_{0} \odot \boldsymbol{\epsilon},
\boldsymbol{\epsilon} \sim \mathcal{N}(0, I)
\end{equation}
$$
后续的隐变量计算则是：
$$
\begin{equation}
\mathbf{z}_{t}=\boldsymbol{\mu}_{t}+\boldsymbol{\sigma}_{t} \odot \mathbf{z}_{t-1}
\end{equation}
$$
其中 $\boldsymbol{\mu}_t, \boldsymbol{\sigma}_{t}$ 是以 $ \mathbf{z}_{t-1}$ 为输入。会发现 $\frac{d\mathbf{z}_t}{d\mathbf{z}_{t-1}}$ 是一个三角矩阵（这里其实要注意求导的方式），而且行列式计算是 $\begin{equation}
\prod_{i=1}^{D} \sigma_{t, i}
\end{equation}$

根据 (5) 式，最后的概率变成：
$$
\begin{equation}
\log q\left(\mathbf{z}_{T} \mid \mathbf{x}\right)=-\sum_{i=1}^{D}\left(\frac{1}{2} \epsilon_{i}^{2}+\frac{1}{2} \log (2 \pi)+\sum_{t=0}^{T} \log \sigma_{t, i}\right)
\end{equation}
$$
实际中，作者并不是用 (10) 的形式。第一，encoder 网络不仅输出 $\boldsymbol{\mu}_0, \boldsymbol{\sigma}_0$ 还有一个变量 $\mathbf{h}$，第二，$\boldsymbol{\mu}_t$ 用另一种形式表示。
$$
\begin{equation}
\left[\mathbf{m}_{t}, \mathbf{s}_{t}\right] \leftarrow \text { AutoregressiveNN }[t]\left(\mathbf{z}_{t}, \mathbf{h} ; \boldsymbol{\theta}\right)
\end{equation}
$$

$$
\begin{equation}
\begin{aligned}
\boldsymbol{\sigma}_{t} &=\operatorname{sigmoid}\left(\mathbf{s}_{t}\right) \\
\mathbf{z}_{t} &=\boldsymbol{\sigma}_{t} \odot \mathbf{z}_{t-1}+\left(1-\boldsymbol{\sigma}_{t}\right) \odot \mathbf{m}_{t}
\end{aligned}
\end{equation}
$$

这个形式的目的是使其更像一个 LSTM 的形式（forget gate bias）。

#### 其他的细节

自回归网络有一些特别的初始化，即 $\mathbf{s}_t$ 接近于 + 1 或 +2

自回归网络的选择：两种，一个是 masked autoregressive networks，一个是 convolutional autoregressive autoencoders。后者针对的是 CIFAR-10 数据集。

在自回归模型中，信号的顺序是需要考虑的，作者在执行 flow 的每一步的时候，会交替使用相反的顺序。这相当于多了 permutation 层，当然这个变换的行列式的对数值为 0.



### 实验

作者没有和 Real NVP 比较。

没有写具体的 inference model 和 generative model 的网络结构。（在附录里）

MNIST 的实验，用的是 convolutional VAE as in (Salimans et al., 2014) with ResNet (He et al., 2015) blocks

CIFAR-10 的实验，用的是 novel architecture, ResNet VAE

从图示来看，这里的 VAE 是 multiple latent variable 的结构，每个 level 的隐变量都用 IAF 

![屏幕快照 2020-12-16 下午8.25.25](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-16 下午8.25.25.png)

( NVAE 就是这个做法，这么一来，实际上也没明确 $\mathbf{z}_T$ 的分布，按照分布变换，$\mathbf{z}_T$ 肯定不是正态分布了，那么此时先验的分布 $\mathbf{z}_T$ 呢？VAE 是怎么计算的呢？这也是 NVAE 论文中让人奇怪的地方)

