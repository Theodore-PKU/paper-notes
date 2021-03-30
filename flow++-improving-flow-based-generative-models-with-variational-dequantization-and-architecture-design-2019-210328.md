# Flow++: Improving Flow-Based Generative Models with Variational Dequantization and Architecture Design

[TOC]

## 信息

Authors: Jonathan Ho, Xi Chen, Aravind Srinivas, Yan Duan, Pieter Abbeel

Year: ICML 2019

Bibtex

```
@inproceedings{ho2019flow++,
  title={Flow++: Improving flow-based generative models with variational dequantization and architecture design},
  author={Ho, Jonathan and Chen, Xi and Srinivas, Aravind and Duan, Yan and Abbeel, Pieter},
  booktitle={International Conference on Machine Learning},
  pages={2722--2730},
  year={2019},
  organization={PMLR}
}
```

cite: 115



## 概括

这篇文章的主要内容是对 flow 模型的改进。作者考虑了三个方面：1. dequantization，2. affine coupling layer，3. 卷积层。对于第一点，作者提出了一种新的 dequantization 的方式。对于第二点，作者给出了一个基于 CDF 的新的非线性组织方式。对于第三点，作者在神经网络的部分增加了 self-attention 层。作者的实验主要是分辨率较小的图像数据集，并做了消融实验验证这三个方面的改进是有效的。这篇文章在当时是一个 sota 模型。



## code

https://github.com/aravindsrinivas/flowpp



## 方法

仍然从传统的 flow 模型开始：$\begin{equation}
f(\mathbf{x})=f_{1} \circ \cdots \circ f_{L}(\mathbf{x})
\end{equation}$

损失函数的构造是：
$$
\begin{equation}
\log p(\mathbf{x})=\log \mathcal{N}(f(\mathbf{x}) ; \mathbf{0}, \mathbf{I})+\sum_{i=1}^{L} \log \left|\operatorname{det} \frac{\partial f_{i}}{\partial f_{i-1}}\right|
\end{equation}
$$
**第一个改进：dequantizaztion**

常见的数据集图像是离散值，通常会先 dequantization 变成连续值，然后再训练模型。dequantization 的常见做法是 adding 均匀噪声，使得不同像素离散点之间不是间隔 1。如果像素值是 0～255，那么噪声的范围就是 0～1。

此时训练模型的就不是原始的数据了，因此损失函数有如下的关系：
$$
\begin{equation}
\begin{array}{l}
\mathbb{E}_{\mathbf{y} \sim p_{\text {data }}}\left[\log p_{\text {model }}(\mathbf{y})\right] \\
=\sum_{\mathbf{x}} P_{\text {data }}(\mathbf{x}) \int_{[0,1)^{D}} \log p_{\text {model }}(\mathbf{x}+\mathbf{u}) d \mathbf{u} \\
\leq \sum_{\mathbf{x}} P_{\text {data }}(\mathbf{x}) \log \int_{[0,1)^{D}} p_{\text {model }}(\mathbf{x}+\mathbf{u}) d \mathbf{u} \\
=\mathbb{E}_{\mathbf{x} \sim P_{\text {data }}}\left[\log P_{\text {model }}(\mathbf{x})\right]
\end{array}
\end{equation}
$$
这里的 y 就是 dequantization 的结果。结论是这样的训练，实际是原始目标函数的一个下界。

作者的改进就是对于噪声 u 的建模，作者用一个条件 flow 模型来建模，条件是 x，$q(u|x)$，u 的值域仍然是 [0,1)，此时，损失函数的关系就是另外一个结果：
$$
\begin{equation}
\begin{array}{l}
\mathbb{E}_{\mathbf{x} \sim P_{\text {data }}}\left[\log P_{\text {model }}(\mathbf{x})\right] \\
=\mathbb{E}_{\mathbf{x} \sim P_{\text {data }}}\left[\log \int_{[0,1)^{D}} q(\mathbf{u} \mid \mathbf{x}) \frac{p_{\text {model }}(\mathbf{x}+\mathbf{u})}{q(\mathbf{u} \mid \mathbf{x})} d \mathbf{u}\right] \\
\geq \mathbb{E}_{\mathbf{x} \sim P_{\text {data }}}\left[\int_{[0,1)^{D}} q(\mathbf{u} \mid \mathbf{x}) \log \frac{p_{\text {model }}(\mathbf{x}+\mathbf{u})}{q(\mathbf{u} \mid \mathbf{x})} d \mathbf{u}\right] \\
=\mathbb{E}_{\mathbf{x} \sim P_{\text {data }}} \mathbb{E}_{\mathbf{u} \sim q(\cdot \mid \mathbf{x})}\left[\log \frac{p_{\text {model }}(\mathbf{x}+\mathbf{u})}{q(\mathbf{u} \mid \mathbf{x})}\right]
\end{array}
\end{equation}
$$
仍然是一个下界。对于 u 的建模是 $\begin{equation}
\mathbf{u}=q_{\mathbf{x}}(\boldsymbol{\epsilon})
\end{equation}$, $\epsilon$ 是高斯噪声。这样(3)就变成了：
$$
\begin{equation}
\begin{array}{l}
\mathbb{E}_{\mathbf{x} \sim P_{\text {data }}}\left[\log P_{\text {model }}(\mathbf{x})\right] \\
\geq \mathbb{E}_{\mathbf{x} \sim P_{\text {data }}, \boldsymbol{\epsilon} \sim p}\left[\log \frac{p_{\text {model }}\left(\mathbf{x}+q_{\mathbf{x}}(\boldsymbol{\epsilon})\right)}{p(\boldsymbol{\epsilon})\left|\partial q_{\mathbf{x}} / \partial \boldsymbol{\epsilon}\right|^{-1}}\right]
\end{array}
\end{equation}
$$
作者这样处理的目的是让 dequantization 的形式更灵活。原来的方式是改进后的一个特例。作者还对此进行了一些分析，不过我觉得不是很重要。

**第二个改进：coupling layers** 

常见的做法是：
$$
\begin{equation}
\mathbf{y}_{1}=\mathbf{x}_{1}, \quad \mathbf{y}_{2}=\mathbf{x}_{2} \cdot \exp \left(\mathbf{a}_{\theta}\left(\mathbf{x}_{1}\right)\right)+\mathbf{b}_{\theta}\left(\mathbf{x}_{1}\right)
\end{equation}
$$
作者的改进：
$$
\begin{equation}
\begin{aligned}
\mathbf{y}_{1} &=\mathbf{x}_{1} \\
\mathbf{y}_{2} &=\sigma^{-1}\left(\operatorname{MixLog} \operatorname{CDF}\left(\mathbf{x}_{2} ; \boldsymbol{\pi}_{\theta}\left(\mathbf{x}_{1}\right), \boldsymbol{\mu}_{\theta}\left(\mathbf{x}_{1}\right), \mathbf{s}_{\theta}\left(\mathbf{x}_{1}\right)\right)\right) \\
& \cdot \exp \left(\mathbf{a}_{\theta}\left(\mathbf{x}_{1}\right)\right)+\mathbf{b}_{\theta}\left(\mathbf{x}_{1}\right)
\end{aligned}
\end{equation}
$$

$$
\begin{equation}
\operatorname{MixLogCDF}(x ; \boldsymbol{\pi}, \boldsymbol{\mu}, \mathbf{s}):=\sum_{i=1}^{K} \pi_{i} \sigma\left(\left(x-\mu_{i}\right) \cdot \exp \left(-s_{i}\right)\right)
\end{equation}
$$

这里的 $\mu$，$s$, $\pi$，$a$，$b$ 都是用网络根据 $x_1$ 计算出来的，$\sigma$ 好像是 sigmoid 函数？不过为什么要用这个形式，我并不理解，是因为这个更复杂吗？

**第三个改进：卷积层**

之前的网络，只使用了卷积层，作者在这篇文章考虑使用 self-attention。具体的结构如下：

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-30 上午11.15.31.png" alt="屏幕快照 2021-03-30 上午11.15.31" style="zoom:50%;" />

where Gate refers to a 1 × 1 convolution that doubles the number of channels, followed by a gated linear unit

另外，卷积层和 PixelCNN++ 的一样（我不知道为什么要这么做）

**实验**

实验数据集是 CIFAR-10，32x32，64x64的 ImageNet. flow 模型的基本结构没有什么特别的地方。和其他 VAE、flow模型、自回归模型相比，在 bits/dim 指标上都是最好的。

消融实验的结果

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-30 上午11.19.20.png" alt="屏幕快照 2021-03-30 上午11.19.20" style="zoom:50%;" />

不过作者给出的生成结果，我觉得一点都不行。



## 反思

这篇文章给出了 flow 模型的三个改进，但效果仍然有限。我认为，这说明这并不是 flow 模型真正有效的改进。也许 flow 模型本身是有问题的？因为 Jacobian 矩阵的限制太强了？



## 参考文献

自回归相关：

van den Oord, A., Kalchbrenner, N., and Kavukcuoglu, K. Pixel recurrent neural networks. International Conference on Machine Learning (ICML), 2016b.

Salimans, T., Karpathy, A., Chen, X., and Kingma, D. P. Pixelcnn++: Improving the pixelcnn with discretized logistic mixture likelihood and other modiﬁcations. arXiv preprint arXiv:1701.05517, 2017.

Chen, X., Mishra, N., Rohaninejad, M., and Abbeel, P. Pixelsnail: An improved autoregressive generative model. arXiv preprint arXiv:1712.09763, 2017.

Parmar, N., Vaswani, A., Uszkoreit, J., Kaiser, Ł., Shazeer, N., and Ku, A. Image transformer. arXiv preprint arXiv:1802.05751, 2018.

其他：

Huang, C.-W., Krueger, D., Lacoste, A., and Courville, A. Neural autoregressive ﬂows. In International Conference on Machine Learning, pp. 2083–2092, 2018.

Kingma, D. P., Salimans, T., and Welling, M. Improving variational inference with inverse autoregressive ﬂow. arXiv preprint arXiv:1606.04934, 2016.