# Denoising Diffusion Probabilistic Models

[TOC]

## 信息

Author: Jonathan Ho, Ajay Jain, Pieter Abbeel

Year: NeurIPS 2020

Bibtex:

```latex
@article{ho2020denoising,
  title={Denoising diffusion probabilistic models},
  author={Ho, Jonathan and Jain, Ajay and Abbeel, Pieter},
  journal={arXiv preprint arXiv:2006.11239},
  year={2020}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/denoising-diffusion-probabilistic-models.pdf)

code: https://github.com/hojonathanho/diffusion



## 概括

这篇文章提出了 DDPM 模型，基于扩散模型，利用其逆过程实现图像生成。作者通过对扩散过程的特殊参数化，建立了 DDPM 和 score matching 以及朗之万动态采样之间的关系。作者的实验表明，DDPM 模型可以生成高质量的图像，不过在似然概率方面，还不够好。



## 方法

作者首先给出了扩散模型的基本想法。

### 扩散模型

扩散模型本质上还是一个参数的隐变量模型。假设扩散过程是一个马尔科夫链，逐渐将噪声加入到数据中，这个过程可以写为：
$$
q\left(\mathbf{x}_{1: T} \mid \mathbf{x}_{0}\right):=\prod_{t=1}^{T} q\left(\mathbf{x}_{t} \mid \mathbf{x}_{t-1}\right), \quad q\left(\mathbf{x}_{t} \mid \mathbf{x}_{t-1}\right):=\mathcal{N}\left(\mathbf{x}_{t} ; \sqrt{1-\beta_{t}} \mathbf{x}_{t-1}, \beta_{t} \mathbf{I}\right)
$$
这里用 q 表示这个过程是要估计的目标。对于生成模型，扩散模型就是用一个逆的马尔科夫链过程来表示，同样的，也假设为高斯分布的形式。这里用 $p_{\theta}$ 表示带参数的分布。
$$
p_{\theta}\left(\mathbf{x}_{0: T}\right):=p\left(\mathbf{x}_{T}\right) \prod_{t=1}^{T} p_{\theta}\left(\mathbf{x}_{t-1} \mid \mathbf{x}_{t}\right), \quad p_{\theta}\left(\mathbf{x}_{t-1} \mid \mathbf{x}_{t}\right):=\mathcal{N}\left(\mathbf{x}_{t-1} ; \boldsymbol{\mu}_{\theta}\left(\mathbf{x}_{t}, t\right), \mathbf{\Sigma}_{\theta}\left(\mathbf{x}_{t}, t\right)\right)
$$
之所以这样做是有用的，可能是因为当 beta 非常小的时候，可以证明 $p_{\theta}$ 是高斯的（应该要参考这篇文章 Jascha Sohl-Dickstein, Eric Weiss, Niru Maheswaranathan, and Surya Ganguli. Deep unsupervised learning using nonequilibrium thermodynamics. In International Conference on Machine Learning, pages 2256–2265, 2015.）。利用这个假设，可以得到关于 $x$ 的概率：
$$
p_{\theta}\left(\mathbf{x}_{0}\right):=\int p_{\theta}\left(\mathbf{x}_{0: T}\right) d \mathbf{x}_{1: T}
$$
生成模型的损失函数往往选择极小化似然函数值，同样的，扩散模型推出的结果是：
$$
\mathbb{E}\left[-\log p_{\theta}\left(\mathbf{x}_{0}\right)\right] \leq \mathbb{E}_{q}\left[-\log \frac{p_{\theta}\left(\mathbf{x}_{0: T}\right)}{q\left(\mathbf{x}_{1: T} \mid \mathbf{x}_{0}\right)}\right]=\mathbb{E}_{q}\left[-\log p\left(\mathbf{x}_{T}\right)-\sum_{t \geq 1} \log \frac{p_{\theta}\left(\mathbf{x}_{t-1} \mid \mathbf{x}_{t}\right)}{q\left(\mathbf{x}_{t} \mid \mathbf{x}_{t-1}\right)}\right]=: L
$$
这个式子用的是似然函数的下界。为了计算 L，给出了一个 L 的新的表达形式：
$$
\mathbb{E}_{q}[\underbrace{D_{\mathrm{KL}}\left(q\left(\mathbf{x}_{T} \mid \mathbf{x}_{0}\right) \| p\left(\mathbf{x}_{T}\right)\right)}_{L_{T}}+\sum_{t>1} \underbrace{D_{\mathrm{KL}}\left(q\left(\mathbf{x}_{t-1} \mid \mathbf{x}_{t}, \mathbf{x}_{0}\right) \| p_{\theta}\left(\mathbf{x}_{t-1} \mid \mathbf{x}_{t}\right)\right)}_{L_{t-1}} \underbrace{-\log p_{\theta}\left(\mathbf{x}_{0} \mid \mathbf{x}_{1}\right)}_{L_{0}}]
$$
在这个式子中，$q\left(\mathbf{x}_{t-1} \mid \mathbf{x}_{t}, \mathbf{x}_{0}\right)$ 满足：
$$
\begin{aligned}
q\left(\mathbf{x}_{t-1} \mid \mathbf{x}_{t}, \mathbf{x}_{0}\right) &=\mathcal{N}\left(\mathbf{x}_{t-1} ; \tilde{\boldsymbol{\mu}}_{t}\left(\mathbf{x}_{t}, \mathbf{x}_{0}\right), \tilde{\beta}_{t} \mathbf{I}\right) \\
\text { where } \quad \tilde{\boldsymbol{\mu}}_{t}\left(\mathbf{x}_{t}, \mathbf{x}_{0}\right) &:=\frac{\sqrt{\bar{\alpha}_{t-1}} \beta_{t}}{1-\bar{\alpha}_{t}} \mathbf{x}_{0}+\frac{\sqrt{\alpha_{t}}\left(1-\bar{\alpha}_{t-1}\right)}{1-\bar{\alpha}_{t}} \mathbf{x}_{t} \quad \text { and } \quad \tilde{\beta}_{t}:=\frac{1-\bar{\alpha}_{t-1}}{1-\bar{\alpha}_{t}} \beta_{t}
\end{aligned}
$$
损失函数的主体其实是 $L_{t-1}$ 的部分。

另外，我们还可以推出：
$$
q\left(\mathbf{x}_{t} \mid \mathbf{x}_{0}\right)=\mathcal{N}\left(\mathbf{x}_{t} ; \sqrt{\bar{\alpha}_{t}} \mathbf{x}_{0},\left(1-\bar{\alpha}_{t}\right) \mathbf{I}\right)
$$
其中：$\alpha_{t}:=1-\beta_{t} \text { and } \bar{\alpha}_{t}:=\prod_{s=1}^{t} \alpha_{s}$。

从以上的分析，我们得到了一个新的损失函数（5）式，并且得到两个额外的表达式，这两个表达式都是由扩散过程确定的。损失函数（5）本质是要让 $p_{\theta}$ 和 q 之间达成某种关系（我感觉这个部分的推导得看 2015 年的那篇论文）。

对于扩散模型，只要对 $p_{\theta}$ 进行建模，设置可以对 $\beta$ 进行训练。

### DDPM 模型

DDPM 模型的本质就是扩散模型，但是，作者给出了一些更详细的要求。

首先，作者假设了 $p_{\theta}$ 中的协方差矩阵是 $\sigma^2_t \mathrm{I}$，$\sigma^2_t$ 的取值有两种，$\sigma_{t}^{2}=\beta_{t}$ 或 $\sigma_{t}^{2}=\tilde{\beta}_{t}=\frac{1-\bar{\alpha}_{t-1}}{1-\bar{\alpha}_{t}} \beta$ ，在这种情况下，$L_{t-1}$ 被大大化简了。变成：
$$
L_{t-1}=\mathbb{E}_{q}\left[\frac{1}{2 \sigma_{t}^{2}}\left\|\tilde{\boldsymbol{\mu}}_{t}\left(\mathbf{x}_{t}, \mathbf{x}_{0}\right)-\boldsymbol{\mu}_{\theta}\left(\mathbf{x}_{t}, t\right)\right\|^{2}\right]+C
$$
更进一步，作者利用 (6) 式，我们可以将 $x_t$ 用 $x_0$ 和噪声来表示，即 $\mathbf{x}_{t}\left(\mathbf{x}_{0}, \boldsymbol{\epsilon}\right)=\sqrt{\bar{\alpha}_{t}} \mathbf{x}_{0}+\sqrt{1-\bar{\alpha}_{t}} \boldsymbol{\epsilon} \text { for } \boldsymbol{\epsilon} \sim \mathcal{N}(\mathbf{0}, \mathbf{I})$。把这个式子带入到 $L_{t-1}$ 中，我们可以得到一个更简单的损失函数：
$$
\begin{aligned}
L_{t-1}-C &=\mathbb{E}_{\mathbf{x}_{0}, \boldsymbol{\epsilon}}\left[\frac{1}{2 \sigma_{t}^{2}}\left\|\tilde{\boldsymbol{\mu}}_{t}\left(\mathbf{x}_{t}\left(\mathbf{x}_{0}, \boldsymbol{\epsilon}\right), \frac{1}{\sqrt{\bar{\alpha}_{t}}}\left(\mathbf{x}_{t}\left(\mathbf{x}_{0}, \boldsymbol{\epsilon}\right)-\sqrt{1-\bar{\alpha}_{t}} \boldsymbol{\epsilon}\right)\right)-\boldsymbol{\mu}_{\theta}\left(\mathbf{x}_{t}\left(\mathbf{x}_{0}, \boldsymbol{\epsilon}\right), t\right)\right\|^{2}\right] \\
&=\mathbb{E}_{\mathbf{x}_{0}, \boldsymbol{\epsilon}}\left[\frac{1}{2 \sigma_{t}^{2}}\left\|\frac{1}{\sqrt{\alpha_{t}}}\left(\mathbf{x}_{t}\left(\mathbf{x}_{0}, \boldsymbol{\epsilon}\right)-\frac{\beta_{t}}{\sqrt{1-\bar{\alpha}_{t}}} \boldsymbol{\epsilon}\right)-\boldsymbol{\mu}_{\theta}\left(\mathbf{x}_{t}\left(\mathbf{x}_{0}, \boldsymbol{\epsilon}\right), t\right)\right\|^{2}\right]
\end{aligned}
$$
此时，由于 $x_t$ 实际上是已知的（因为噪声已知），$\mu_{\theta}$ 就可以表示为：$\boldsymbol{\mu}_{\theta}\left(\mathbf{x}_{t}, t\right)=\tilde{\boldsymbol{\mu}}_{t}\left(\mathbf{x}_{t}, \frac{1}{\sqrt{\bar{\alpha}_{t}}}\left(\mathbf{x}_{t}-\sqrt{1-\bar{\alpha}_{t}} \boldsymbol{\epsilon}_{\theta}\left(\mathbf{x}_{t}\right)\right)\right)=\frac{1}{\sqrt{\alpha_{t}}}\left(\mathbf{x}_{t}-\frac{\beta_{t}}{\sqrt{1-\bar{\alpha}_{t}}} \boldsymbol{\epsilon}_{\theta}\left(\mathbf{x}_{t}, t\right)\right)$

所以，最后 $L_{t-1}$ 就变成：
$$
\mathbb{E}_{\mathbf{x}_{0}, \boldsymbol{\epsilon}}\left[\frac{\beta_{t}^{2}}{2 \sigma_{t}^{2} \alpha_{t}\left(1-\bar{\alpha}_{t}\right)}\left\|\boldsymbol{\epsilon}-\boldsymbol{\epsilon}_{\theta}\left(\sqrt{\bar{\alpha}_{t}} \mathbf{x}_{0}+\sqrt{1-\bar{\alpha}_{t}} \boldsymbol{\epsilon}, t\right)\right\|^{2}\right]
$$
而这个式子就是要用一个网络来估计噪声。这个形式就和 denoising score matching 一致。这是 DDPM 的基础。

注意，我们现在仅仅解决了 $L_{t-1}$，$L_0$ 还没有解决。作者的假设是 $x_0$ 是图像，所以是离散值，并且归一化到 [-1, 1] 之间。由于 $x_0$ 是离散的，但是 $p_\theta(x_0|x_1)$  计算的 $x_0$ 不是离散的，是连续的，因此作者对该分布进行积分来计算离散值的概率。这个积分为：
$$
\begin{aligned}
p_{\theta}\left(\mathbf{x}_{0} \mid \mathbf{x}_{1}\right) &=\prod_{i=1}^{D} \int_{\delta_{-}\left(x_{0}^{i}\right)}^{\delta_{+}\left(x_{0}^{i}\right)} \mathcal{N}\left(x ; \mu_{\theta}^{i}\left(\mathbf{x}_{1}, 1\right), \sigma_{1}^{2}\right) d x \\
\delta_{+}(x) &=\left\{\begin{array}{ll}
\infty & \text { if } x=1 \\
x+\frac{1}{255} & \text { if } x<1
\end{array} \quad \delta_{-}(x)= \begin{cases}-\infty & \text { if } x=-1 \\
x-\frac{1}{255} & \text { if } x>-1\end{cases} \right.
\end{aligned}
$$
作者表示，这样做得好处是不需要对于数据进行连续化，可以直接算出离散值对应的结果。$L_T$ 因为 beta 是固定的，所以不需要计算了。

但是作者最后给出了一个更简单的损失函数：
$$
L_{\text {simple }}(\theta):=\mathbb{E}_{t, \mathbf{x}_{0}, \boldsymbol{\epsilon}}\left[\left\|\boldsymbol{\epsilon}-\boldsymbol{\epsilon}_{\theta}\left(\sqrt{\bar{\alpha}}_{t} \mathbf{x}_{0}+\sqrt{1-\bar{\alpha}_{t}} \boldsymbol{\epsilon}, t\right)\right\|^{2}\right]
$$
在这里，$t=1$ 对应 $L_0$ （但是我没看出来为什么对应），$t>1$ 则是去掉了权重。关于时间 t，是从 1 到 T 随机抽样的。

作者表示，这样做可以让网络训练更集中于困难的去噪任务，而且显示了更好的采样质量。

训练和采样的算法：

![屏幕快照 2021-08-06 下午2.33.02](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-06 下午2.33.02.png)



## 实验

作者设 T = 1000，beta 的变化是从 $1e-4$ 到 0.02 （线性）。这个结果保障了 $q(X_T|x_0)$ 和标准正态分布非常接近。

U-Net 骨架的网络用来表示模型。使用了 group normalization。时间 t 通过 Transformer sinusoidal position embedding [60]（Ashish Vaswani, Noam Shazeer, Niki Parmar, Jakob Uszkoreit, Llion Jones, Aidan N Gomez, Łukasz Kaiser, and Illia Polosukhin. Attention is all you need. In Advances in Neural Information Processing Systems, pages 5998–6008, 2017.） 作为网络的输入。在16x16 分辨率的位置使用了 self-attention。更多细节参考附录 B

### 评价指标

作者计算了 IS 和 FID，FID 在训练集和测试集都计算了（为什么？有什么差别？）。

如果用简化后的目标函数，训练的模型的生成质量更好，但是如果用原始版本的损失函数（权重没有改）那么产生 better codelengths（这个应该和似然概率有关）。

### 实验结果

![屏幕快照 2021-08-06 下午2.37.06](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-06 下午2.37.06.png)

这个图给出了不同的损失函数和参数化策略（是否要固定协方差矩阵）的实验结果。

NLL 的结果（CIFAR10）比大多数模型好，但是不如 sparse transformer [7]（这篇似乎是自回归的方法）

另外这篇文章在第四节还设计了不少实验，显示这个模型的有趣的特性。此处不赘述。



## 参考文献

Jascha Sohl-Dickstein, Eric Weiss, Niru Maheswaranathan, and Surya Ganguli. Deep unsupervised learning using nonequilibrium thermodynamics. In International Conference on Machine Learning, pages 2256–2265, 2015.

Ashish Vaswani, Noam Shazeer, Niki Parmar, Jakob Uszkoreit, Llion Jones, Aidan N Gomez, Łukasz Kaiser, and Illia Polosukhin. Attention is all you need. In Advances in Neural Information Processing Systems, pages 5998–6008, 2017.

Rewon Child, Scott Gray, Alec Radford, and Ilya Sutskever. Generating long sequences with sparse transformers. arXiv preprint arXiv:1904.10509, 2019.