### Paper:

Learning Generative Models using Denoising Density Estimators

### Author:

Anonymous authors. ICLR 2020 under review

### Year:

2020

### Notes:

在这篇文章中，作者提出的主要想法是通过神经网络估计的 $\log \tilde{p}(x)$ 来生成图像。整个生成过程包含3个部分。一个是用训练数据得到的 $\log \tilde{p}(x)$， 一个是训练生成数据的 $\log \tilde{q}(x)$ ，还有一个是用于生成图像的 $g(z), z \sim \mathcal{N}(0,1)$。而 $\log \tilde{p}(x)$ 的训练过程和去噪有很大的关系。

 首先建立 $\log \tilde{p}(x)$ 和去噪的关系。假设有一个去噪模型 $r$，噪声为 $\eta \sim \mathcal{N}(0,\sigma_{\eta})$，那么损失函数为：
$$
\mathcal{L}_{\mathrm{DAE}}\left(r ; p, \sigma_{\eta}\right)=\mathbb{E}_{x \sim p, \eta \sim \mathcal{N}\left(0, \sigma_{\eta}^{2}\right)}\left[\|r(x+\eta)-x\|^{2}\right]
$$
已有的结论是，对于最优的去噪模型 $r^*(x)$ 有如下关系：
$$
r^{*}(x)=x+\sigma_{\eta}^{2} \nabla_{x} \log \tilde{p}(x)\\
\tilde{p}(x)  表示加了噪声之后的概率分布。
$$
假设 $f(x+\eta)$ 是一个估计 $-\eta/\sigma_{\eta}^2$ 的函数，并且用极小化 $\mathbb{E}_{x \sim p, \eta \sim \mathcal{N}\left(0, \sigma_{\eta}^{2}\right)}\left[\left\|f(x+\eta)+\eta / \sigma_{\eta}^{2}\right\|^{2}\right]$ 得到，那么，最优的 $f*(x) = \nabla_x \log \tilde{p}(x)$.

假设 $s(x) = \log\tilde{p}(x) + C$，那么根据(2)，$\nabla_x s(x+\eta) = (r^*(x) - x)/\sigma_{\eta}^2 =(在期望意义上) \eta/\sigma_{\eta}^2$，或者说 $s^*(x) = \log\tilde{p}(x) + C$ 必然是 $\mathbb{E}_{x \sim p, \eta \sim \mathcal{N}\left(0, \sigma_{\eta}^{2}\right)}\left[\left\|\nabla_{x} s(x+\eta)+\eta / \sigma_{\eta}^{2}\right\|^{2}\right]$ 的最优解。

所以作者的想法就是用神经网路得到 $s(x)$，也就得到了 $\log \tilde{p}(x)$，如果我们有一个生成网络 $g(z)$ 可以生成图像，又有一个生成图像的概率的表达 $s_q(x) = \log\tilde{q}(x)$，那么 利用 KL 散度，极小化 $D_{\mathrm{KL}}(\tilde{q} \| \tilde{p})$ 就可以得到一个好的生成模型。

其实这里的 $s(x)$ 就是能量函数，和 [Learning Energy-Based Models in High-dimensional Spaces with Multi-scale Denoising Score Matching](Learning-energy-based-models-in-high-dimensianl-spaces-with-multi-scale-denoising-score-matching.md) 的能量函数是一样的.

所以作者的整体做法就是：

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-16%E4%B8%8B%E5%8D%882.05.50.png)

总结起来：现有一个用真实数据训练好的网络 A 表示 $\log \tilde{p}(x)$，对生成网络产生的图像 $g(z)$，通过 $\min \mathbb{E} [s^{\tilde{q}}(g(z)) - \log \tilde{p}(g(z))]$ (这一步可以证明会使得两个分布的 KL 散度下降，但我认为还不够充分，怎么证明最终会收敛到极小值而不是无穷接近某个值，作者说 KL 散度是凸的，但是也没理由证明。不过和对抗loss还是很相近的)来训练生成网络，再用生成网络生成的图像来训练网络 B $s^{\tilde{q}}$.

作者使用的估计 $s(x)$ 的网络结构是多层感知机，有残差结构，使用 softplus 激活函数。

### Bibtex: