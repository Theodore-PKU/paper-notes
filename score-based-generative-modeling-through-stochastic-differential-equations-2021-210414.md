# Score-based Generative Modeling through Stochastic Differential Equantions

[TOC]

## 信息

Authors: Yang Song, Jascha Sohl-Dickstein, Diederik P. Kingma, Abhishek Kumar, Stefano Ermon and Ben Poole

Year: ICLR 2021

Bibtex:

```
@article{song2020score,
  title={Score-Based Generative Modeling through Stochastic Differential Equations},
  author={Song, Yang and Sohl-Dickstein, Jascha and Kingma, Diederik P and Kumar, Abhishek and Ermon, Stefano and Poole, Ben},
  journal={arXiv preprint arXiv:2011.13456},
  year={2020}
}
```

cite: 14



## Code

https://github.com/yang-song/score_sde



## 概括

这篇文章是 ICLR 2021 的 best paper 之一，我认为是一个篇非常好的文章。这篇文章使用了不同于我此前见到的生成模型，并且取得了很不错的效果。不仅可以生成高清的大图，并且在 CIFAR-10 上不论是 FID、IS、NLL（特别是这一个）取得了非常好的效果。在 NLL 上超越了此前的所有方法。这篇文章的方法叫做 score-based 生成模型。这里的 score 指的是图像概率分布关于图像 x 的梯度。score-based 方法简单来说就是实现从图像到噪声的变化，逆过程则是从噪声到图像。这篇文章将以往的 score-based 方法推广到随机微分方程的形式，在更广泛的意义下讨论了这种方法，此前的一些离散的方法都可以看作是随机微分方程的两种特例下的离散化算法。主要的贡献是提出了一种 predictor-corrector 的框架来改进逆过程的演化；建立了随机微分方程和神经常微分方程之间的关系，利用 ODE 有效地计算生成模型的似然函数值，改进采样的效率；另外作者还提供了一种基于 score-based model 解决逆问题的思路，作者考虑了类别条件生成、inpainting、colorization 三种场景。score-based model 的核心之一是估计 score，这是通过一个神经网络来实现的，作者的实验表明，经过改进的神经网络，这个方法可以在 CIFAR-10 上实现目前最好的 likelihood，Inception score、FID 都达到极高的水准。



## 方法

score-based 的方法并不是这篇文章的首创（不过作者之前都在做相关的工作），原来的方法考虑的是一个离散的形式。在这篇文章中，作者把 score-based 推广到了连续的形式，构成了一个随机微分方程（SDE）。SDE 将图像逐渐变换到一个已知的噪声先验分布中，利用 reverse-time SDE，可以将噪声从先验分布中变换到图像中，以此实现图像的生成。

一个直观的例子是下面的图示。

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-14 下午4.48.39.png" alt="屏幕快照 2021-04-14 下午4.48.39" style="zoom:50%;" />

左边是原始的图像，右边是噪声。从左到右，通过一个随机微分方程的演变（逐渐增加噪声），最终会变成一个噪声图像。从右到左，相当于逐渐减少噪声，变成一个高质量图像。

### 背景介绍

先从两个例子讲起，SMLD 和 DDPM.

SMLD 的全称是 Denoising score matching with Langevin dynamics. 假设我们对 x 施加一个零均值的高斯噪声，那么这个带噪声的图片的条件分布就是 $p_\sigma(\tilde{x}|x) \sim \mathcal{N}(x, \sigma^2)$，对整个数据集而言，这个分布就是一个积分 $p_\sigma(\tilde{x}) = \int p_{\text{data}}(x) p_{\sigma}(\tilde{x}|x) dx$. $p_{\text{data}}$ 是数据的分布。考虑 $\sigma$ 从小变到大，$\sigma_{\min} = \sigma_1, \sigma_{\max} = \sigma_N$。如果 $\sigma_1$ 足够小，那么 $p_{\sigma_1}$ 近似就是 $p_{\text{data}}$，如果 $\sigma_N$ 足够大，那么 $p_{\sigma_N}$ 就近似是高斯噪声。定义一个 score 函数 $s_\theta(x, \sigma)$，在 SMLD 这篇文章中，作者使用了一个带权重的 score matching 目标函数：
$$
\boldsymbol{\theta}^{*}=\underset{\boldsymbol{\theta}}{\arg \min } \sum_{i=1}^{N} \sigma_{i}^{2} \mathbb{E}_{p_{\text {data }}(\mathbf{x})} \mathbb{E}_{p_{\sigma_{i}}(\tilde{\mathbf{x}} \mid \mathbf{x})}\left[\left\|\mathbf{s}_{\boldsymbol{\theta}}\left(\tilde{\mathbf{x}}, \sigma_{i}\right)-\nabla_{\tilde{\mathbf{x}}} \log p_{\sigma_{i}}(\tilde{\mathbf{x}} \mid \mathbf{x})\right\|_{2}^{2}\right]
$$
假设有足够多的数据，并且模型足够强大，优化也完全没问题，那么最优的 score-based model $s_{\theta^*}(x, \sigma) = \nabla_x \log p_\sigma(x)$。这里需要额外解释一下。虽然 (1) 看起来 score 函数拟合的是一个后验分布的梯度，但实际上由于 $\tilde{x}$ 本身可以来自其他的 x ，所以这个式子是在平均意义下的最优，而后验分布的梯度的平均，实际就是 $p_{\sigma}(x)$ 的梯度了。我自己给出一个需要指出的推导：
$$
\nabla_\tilde{x} \log p_\sigma(\tilde{x}) = \frac{1}{p_\sigma(\tilde{x})}\nabla_\tilde{x} p_\sigma(\tilde{x}) = \\  \frac{1}{p_\sigma(\tilde{x})} \nabla_\tilde{x} \int p_{\text{data}}(x) p_{\sigma}(\tilde{x}|x) dx = \frac{1}{p_\sigma(\tilde{x})} \int \nabla_\tilde{x}(p_{\text{data}}(x) p_{\sigma}(\tilde{x}|x)) dx \\ = \frac{1}{p_\sigma(\tilde{x})}\int p_{\text{data}}(x) \nabla_\tilde{x}p_{\sigma}(\tilde{x}|x)  dx
$$

$$
s_\theta^*(\tilde{x}, \sigma) =  E_{p_{\text{data}}(x)} E_{p_\sigma(\tilde{x}|x)} \nabla_{\tilde{x}} \log p_{\sigma}(\tilde{x}|x) \\
= \int p_{\text{data}}(x) p_{\sigma}(\tilde{x}|x) \nabla_{\tilde{x}}\log p_{\sigma} (\tilde{x}|x) dx \\
= \int p_{\text{data}}(x) p_{\sigma}(\tilde{x}|x) \frac{1}{p_{\sigma}(\tilde{x}|x)} \nabla_{\tilde{x}} p_{\sigma}(\tilde{x}|x) dx \\
= \int p_{\text{data}}(x) \nabla_{\tilde{x}} p_{\sigma}(\tilde{x}|x) dx
$$

比较 (2) 和 (3) 可知，$s_\theta^*(\tilde{x}, \sigma) = \nabla_\tilde{x} \log p_\sigma(\tilde{x})$。这就证明了 (1) 式的优化是合理的。

当 score-based model 得到之后，采样的方式则是一个 Langevin MCMC 的方法。
$$
\mathbf{x}_{i}^{m}=\mathbf{x}_{i}^{m-1}+\epsilon_{i} \mathbf{S}_{\boldsymbol{\theta}^{*}}\left(\mathbf{x}_{i}^{m-1}, \sigma_{i}\right)+\sqrt{2 \epsilon_{i}} \mathbf{z}_{i}^{m}, \quad m=1,2, \cdots, M
$$
$z^m_i$ 是标准正态分布。这里的顺序是 for i = N to 1 and for m = 1 to M，开头是 $x_N^0 \sim \mathcal{N}(0, \sigma_{\max} I)$，并且 $x_i^0 = x_{i+1}^M$. 只要 M 趋于无穷大，$\epsilon_i$ 趋于无穷小，$x_1^M$ 就会变成 $p_{\sigma_{\min}} (x)$ 的一个采样（满足某些限制条件的情况下，这个可能得看论文？）

DDPM 的全称是 Denoising diffusion probabilistic model。考虑一个正数序列 $0 < \beta_1, ..., \beta_N < 1$,  对任意一个图像 $x_0$ which 服从 $p_{\text{data}}$，构造一个马尔可夫链 $p\left(\mathbf{x}_{i} \mid \mathbf{x}_{i-1}\right)=\mathcal{N}\left(\mathbf{x}_{i} ; \sqrt{1-\beta_{i}} \mathbf{x}_{i-1}, \beta_{i} \mathbf{I}\right)$，那么就可以得到 $x_i$ 关于 $x_0$ 的概率分布 $p_{\alpha_{i}}\left(\mathbf{x}_{i} \mid \mathbf{x}_{0}\right)=\mathcal{N}\left(\mathbf{x}_{i} ; \sqrt{\alpha_{i}} \mathbf{x}_{0},\left(1-\alpha_{i}\right) \mathbf{I}\right)$ 这里的 $\alpha_i = \prod_{j=1}^{i}\left(1-\beta_{j}\right)$. 后验分布的积分就得到 $p_{\alpha_{i}}(\tilde{\mathbf{x}}):=\int p_{\text {data }}(\mathbf{x}) p_{\alpha_{i}}(\tilde{\mathbf{x}} \mid \mathbf{x}) \mathrm{d} \mathbf{x}$。

类似于 SMLD 模型，如果 $\beta$ 的选择足够好，那么 $x_n$ 的概率分布应该是一个标准正态分布。

同样地，训练一个 score-based model，目标函数是：
$$
\boldsymbol{\theta}^{*}=\underset{\boldsymbol{\theta}}{\arg \min } \sum_{i=1}^{N}\left(1-\alpha_{i}\right) \mathbb{E}_{p_{\text {data }}(\mathbf{x})} \mathbb{E}_{p_{\alpha_{i}}(\tilde{\mathbf{x}} \mid \mathbf{x})}\left[\left\|\mathbf{s}_{\boldsymbol{\theta}}(\tilde{\mathbf{x}}, i)-\nabla_{\tilde{\mathbf{x}}} \log p_{\alpha_{i}}(\tilde{\mathbf{x}} \mid \mathbf{x})\right\|_{2}^{2}\right]
$$
很显然，(5) 和 (1) 非常接近，目的都是用来获得 $\nabla_{\mathbf{x}} \log p_{\alpha_{i}}(\mathbf{x})$ 的估计。

采样的时候，采用下列的形式：
$$
\mathbf{x}_{i-1}=\frac{1}{\sqrt{1-\beta_{i}}}\left(\mathbf{x}_{i}+\beta_{i} \mathbf{s}_{\boldsymbol{\theta}^{*}}\left(\mathbf{x}_{i}, i\right)\right)+\sqrt{\beta_{i}} \mathbf{z}_{i}, \quad i=N, N-1, \cdots, 1
$$
这里之所以使用这个形式是因为他们考虑了一个逆的马尔可夫链：$p_{\boldsymbol{\theta}}\left(\mathbf{x}_{i-1} \mid \mathbf{x}_{i}\right)=\mathcal{N}\left(\mathbf{x}_{i-1} ; \frac{1}{\sqrt{1-\beta_{i}}}\left(\mathbf{x}_{i}+\beta_{i} \mathbf{s}_{\boldsymbol{\theta}}\left(\mathbf{x}_{i}, i\right)\right), \beta_{i} \mathbf{I}\right)$

另外，(5) 和 (1) 的权重也有特别的意义：
$$
\sigma_{i}^{2} \propto 1 / \mathbb{E}\left[\left\|\nabla_{\mathbf{x}} \log p_{\sigma_{i}}(\tilde{\mathbf{x}} \mid \mathbf{x})\right\|_{2}^{2}\right] \text { and }\left(1-\alpha_{i}\right) \propto 1 / \mathbb{E}\left[\left\|\nabla_{\mathbf{x}} \log p_{\alpha_{i}}(\tilde{\mathbf{x}} \mid \mathbf{x})\right\|_{2}^{2}\right]
$$
从以上两个例子，我们大致可以明白 score-based model 是如果实现从 x 到噪声的逆过程，并且从噪声采样得到 x。这里的核心就是估计了 $\nabla_x \log p_{\sigma}(x)$，$p_{\sigma}(x)$ 就是 x 到噪声变换过程中的每一个中间值的分布。



### score-based model with SDE

这个部分是将离散的 score-based model 拓展到连续的形式，构成了随机微分方程 SDE

当拓展到连续的情形时，就要引入时间 t。整个 diffusion 的过程就是 $\{x_t\}^{T}_{t=0}$。$p_0$ 就是原始的数据分布，$p_T$ 就是最终结果的分布。既然是一个演化过程，那么用一个 SDE 来表示。
$$
\mathrm{d} \mathbf{x}=\mathbf{f}(\mathbf{x}, t) \mathrm{d} t+g(t) \mathrm{d} \mathbf{w}
$$
之后会介绍 SMLD 和 DDPM 和 (8) 之间的关系。这里的 w 是标准维纳过程。f 是一个向量函数，是一个 drift 系数（表示 x 的偏移？），g 是一个标量函数，表示 diffusion 的系数。理论上，g 可以更复杂，但是这里仅仅考虑标量函数。**附录 A** 中给出了当 g 更复杂的时候相关的一些计算。

The SDE has a unique strong solution as long as the coefﬁcients are globally Lipschitz in both state and time。这句话表示 SDE 存在一个解（这个解应该是说 $x$ 的分布）。

用 $p_t(x)$ 表示 t 时刻的分布。$p_{st}(x_t|x_s)$ 表示一个转换 kernel，通常 $p_T$ 是一个先验分布，和 $x_0$ 无关。

要从 $p_T$ 采样变换到 $p_0$，就需要一个逆的 diffusion 过程。这也是一个 SDE，表示为：
$$
\mathrm{d} \mathbf{x}=\left[\mathbf{f}(\mathbf{x}, t)-g(t)^{2} \nabla_{\mathbf{x}} \log p_{t}(\mathbf{x})\right] \mathrm{d} t+g(t) \mathrm{d} \overline{\mathbf{w}}
$$
这里的 $\bar{w}$ 也是标准维纳过程。看！这里需要知道 $\nabla_x \log p_t(x)$。因此 score-based model 就是用来估计这个梯度。
$$
\boldsymbol{\theta}^{*}=\underset{\boldsymbol{\theta}}{\arg \min } \mathbb{E}_{t}\left\{\lambda(t) \mathbb{E}_{\mathbf{x}(0)} \mathbb{E}_{\mathbf{x}(t) \mid \mathbf{x}(0)}\left[\left\|\mathbf{s}_{\boldsymbol{\theta}}(\mathbf{x}(t), t)-\nabla_{\mathbf{x}(t)} \log p_{0 t}(\mathbf{x}(t) \mid \mathbf{x}(0))\right\|_{2}^{2}\right]\right\}
$$
这里的权重 $\lambda$ 也满足 $\lambda \propto 1 / \mathbb{E}\left[\left\|\nabla_{\mathbf{x}(t)} \log p_{0 t}(\mathbf{x}(t) \mid \mathbf{x}(0))\right\|_{2}^{2}\right]$. 另外作者也提到，除了 (10) 也有其他的估计 $\nabla_x \log p_t(x)$ 的方法。比如 slice score matching，finite-difference score matching

（10）这个目标函数需要知道转换 kernel 的分布，不过如果 SDE 中的 f 是 affine 的，那么转换 kernel 就是高斯分布。如果不知道 kernel 的分布，但是可以从 kernel 分布中 sample，那么用 slice score matching 方法也是可以的。 **附录 A** 介绍了相关的方法。

附录 A 的说明：

在附录 A 中，作者给出了更复杂更一般的 SDE 形式，以及对应的逆 SDE 形式。这种情况是非常复杂的，所以作者这里对 g 进行简化是很有必要的。如果 f 不是 affine 的，附录中给出了 sliced score matching 的目标函数

![屏幕快照 2021-04-14 下午8.37.48](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-14 下午8.37.48.png)

下面说明 SMLD 和 DDPM 于 SDE 的关系。

SMLD：对应的马尔可夫链是
$$
\mathbf{x}_{i}=\mathbf{x}_{i-1}+\sqrt{\sigma_{i}^{2}-\sigma_{i-1}^{2} \mathbf{z}_{i-1}}
$$
这里的 z 都是标准正态分布。把离散的 N 步变成无穷步，推出 SDE
$$
\mathrm{d} \mathbf{x}=\sqrt{\frac{\mathrm{d}\left[\sigma^{2}(t)\right]}{\mathrm{d} t}} \mathrm{~d} \mathbf{w}
$$
DDPM 对应的马尔可夫链是 
$$
\mathbf{x}_{i}=\sqrt{1-\beta_{i}} \mathbf{x}_{i-1}+\sqrt{\beta_{i}} \mathbf{z}_{i-1}, \quad i=1, \cdots, N
$$
所以 SDE 是
$$
\mathrm{d} \mathbf{x}=-\frac{1}{2} \beta(t) \mathbf{x} \mathrm{d} t+\sqrt{\beta(t)} \mathrm{d} \mathbf{w}
$$
由于 SMLD 的 variance 是逐渐变大的，所以称为 Variance Exploding (VE) SDE，DDPM 的 variance 不变，所以称为 Variance Preserving (VP) SDE.

作者自己提出了一个 sub VP SDE（但是不知道这个 idea 是怎么来的），SDE 形式是：
$$
\mathrm{d} \mathbf{x}=-\frac{1}{2} \beta(t) \mathbf{x} \mathrm{d} t+\sqrt{\beta(t)\left(1-e^{-2 \int_{0}^{t} \beta(s) \mathrm{d} s}\right) } \mathrm{d}\mathbf{w}
$$
这三个 SDE 的 f 都是 affine，所以转换kernel 都是高斯的。

附录 B 给出了这三个 SDE 的推导过程（如何从 SMLD 和 DDPM 的形式推出 SDE 的形式）

附录 C 给出了具体的 VE SDE 和 VP SDE 的例子（参数）

### 求解 SDE

有了 $s_\theta$，就意味着逆 SDE 的形式已知。接下来的问题就是怎么实现采样（实际上还是一个离散的采样方式）

一般来说，任意一个数值求解算法都是可以用的，只不过有不同的离散方式。

the sampling method of DDPM 叫做 ancestral sampling，则对应 VP SDE 的某一种离散求解。但是这个方法对于其他的 SDE，推导是困难的，所以作者提出了一个 reverse diffusion samplers**附录 E**

假设 forward SDE 的离散化是：
$$
\mathbf{x}_{i+1}=\mathbf{x}_{i}+\mathbf{f}_{i}\left(\mathbf{x}_{i}\right)+\mathbf{G}_{i} \mathbf{z}_{i}, \quad i=0,1, \cdots, N-1
$$
那么逆过程是：
$$
\mathbf{x}_{i}=\mathbf{x}_{i+1}-\mathbf{f}_{i+1}\left(\mathbf{x}_{i+1}\right)+\mathbf{G}_{i+1} \mathbf{G}_{i+1}^{\top} \mathbf{s}_{\boldsymbol{\theta}^{*}}\left(\mathbf{x}_{i+1}, i+1\right)+\mathbf{G}_{i+1} \mathbf{z}_{i+1}
$$
这个用在算法2和算法3中作为 predictor 的部分。

但是这里用到的 SDE 其实不是一般的 SDE，所以可以用其他的方法来更好地求解（核心原因是 scoare-based model 是已知的，对于任意 t 都知道）。首先一个数值求解器先得到一个预测值（next time step 的 x）然后作者用 score-based MCMC 来改进这个预测结果。所以称为 Predictor-Corrector methods，a family of numerical continuation techniques for solving systems of equations (但是这个不懂)。因此作者称为 Predictor-Corrector (PC) samplers. **附录 G**

PC sampler 在 SMLD 中，就是an identity function as the predictor and annealed Langevin dynamics as the corrector，在 DDPM 中是ancestral sampling as the predictor and identity as the corrector.

![屏幕快照 2021-04-21 下午4.54.48](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-21 下午4.54.48.png)

作者测试了 PC sample 在原来的 SMLD，DDPM 模型（有限步的模型），表示这个采样方式更有效。

### 概率 flow 模型和神经 ODE

score-based 模型是没有办法计算概率的，但是作者说，对于给定的 diffusion 过程，存在一个决定性的变换过程（没有随机性）具有和 SDE 相同的边缘分布 $p_t(x)$
$$
\mathrm{d} \mathbf{x}=\left[\mathbf{f}(\mathbf{x}, t)-\frac{1}{2} g(t)^{2} \nabla_{\mathbf{x}} \log p_{t}(\mathbf{x})\right] \mathrm{d} t
$$
（这里的 g(t) 是简化的版本）

**附录 D.1** 给出了相关的推导（从一般的 SDE 到一个 ODE，但是我还没完全理解这个过程）。这就和 flow 模型联系起来了，只不过这是一个神经 ODE 模型。

但是这个模型已经是固定的了，因为 $\nabla_x \log p_t(x)$ 已经用 SDE 相关的目标函数估计出来了（score-based model），所以直接用 (16) 就可以变成一个可以计算似然概率的 flow 模型，因此可以用来计算 input 图像的概率。**附录 D.2**

概率计算：
$$
\log p_{0}(\mathbf{x}(0))=\log p_{T}(\mathbf{x}(T))+\int_{0}^{T} \nabla \cdot \tilde{\mathbf{f}}_{\boldsymbol{\theta}}(\mathbf{x}(t), t) \mathrm{d} t
$$
但这个式子中  $ \nabla \cdot \tilde{\mathbf{f}}_{\boldsymbol{\theta}}(\mathbf{x}(t), t) $ 是不好算的。$\tilde{f}$ 是 (18) 式 dt 前面的整个部分。所以采用了一种近似的方法 Skilling-Hutchinson trace estimator：
$$
\nabla \cdot \tilde{\mathbf{f}}_{\boldsymbol{\theta}}(\mathbf{x}, t)=\mathbb{E}_{p(\boldsymbol{\epsilon})}\left[\boldsymbol{\epsilon}^{\top} \nabla \tilde{\mathbf{f}}_{\boldsymbol{\theta}}(\mathbf{x}, t) \boldsymbol{\epsilon}\right]
$$
$\epsilon$ 是一个随机变量，服从 0 均值和单位矩阵的协方差。$\epsilon^{\top} \nabla \tilde{f}_{\theta}$ 可以用自动微分来计算（咋算？）。因为这个估计是无偏的，所以可以用计算多个值的平均值。

有了（18）式，就得到了 x 和隐变量（也就是 x_T）之间的一一对应关系。所以就可以对隐变量实现数据操纵，比如 interpolation, and temperature scaling

由于 $p_T$ 的分布是一个和 $x_0$ 无关的先验分布，就可以比较快速的 sample 出图像。这个叫 probability flow sampling
$$
\mathbf{x}_{i}=\mathbf{x}_{i+1}-\mathbf{f}_{i+1}\left(\mathbf{x}_{i+1}\right)+\frac{1}{2} \mathbf{G}_{i+1} \mathbf{G}_{i+1}^{\top} \mathbf{s}_{\boldsymbol{\theta}^{*}}\left(\mathbf{x}_{i+1}, i+1\right), \quad i=0,1, \cdots, N-1,
$$
如果用 black-box ODE solver，还可以减少 sample 的步数，提高效率（同时图像质量不会下降太多）。这里的 black-box solver 是 RK45 ODE solver。

但是概率flow模型如果不用 corrector，效果都没有那么好。

### 模型的改进

在正文中没有提到具体的改进，仅仅是比较了不同模型的结果。

在这篇文章中，比较的东西是很多的，不同的 SDE，是否使用 SDE（还是原始的离散形式计算 score-based model），使用什么样的采样过程，是否使用概率 flow ODE 模型（以及是否使用了 black-box ODE solver）

**附录 H**

Upsampling and downsampling images with anti-aliasing based on Finite Impulse Response (FIR) (Zhang, 2019). We follow the same implementation and hyper-parameters in StyleGAN-2 (Karras et al., 2020b).

Rescaling all skip connections by $1/\sqrt{2}$, This has been demonstrated effective in several bestin-class GAN models, including ProgressiveGAN (Karras et al., 2018), StyleGAN (Karras et al., 2019) and StyleGAN-2 (Karras et al., 2020b).

Replacing the original residual blocks in DDPM with residual blocks from BigGAN

Increasing the number of residual blocks per resolution from 2 to 4.

Incorporating progressive growing architectures. We consider two progressive architectures for input: “input skip” and “residual”, and two progressive architectures for output: “output skip” and “residual”. These progressive architectures are deﬁned and implemented according to StyleGAN-2.

感觉借鉴了很多 GAN 的内容。



### 逆问题

关于逆问题，作者的思路是从 $p_T(x(T)|y)$ 开始 sample
$$
\mathrm{d} \mathbf{x}=\left\{\mathbf{f}(\mathbf{x}, t)-g(t)^{2}\left[\nabla_{\mathbf{x}} \log p_{t}(\mathbf{x})+\nabla_{\mathbf{x}} \log p_{t}(\mathbf{y} \mid \mathbf{x})\right]\right\} \mathrm{d} t+g(t) \mathrm{d} \overline{\mathbf{w}}
$$
这里需要额外得到 $\nabla_{\mathbf{x}} \log p_{t}(\mathbf{y} \mid \mathbf{x}(t))$。具体参见 **附录 I**

这个怎么算呢？因为 $p_{t}(\mathbf{x}(t) \mid \mathbf{y}) \propto p_{t}(\mathbf{x}(t)) p(\mathbf{y} \mid \mathbf{x}(t))$，所以有：
$$
\nabla_{\mathbf{x}} \log p_{t}(\mathbf{x}(t) \mid \mathbf{y})=\nabla_{\mathbf{x}} \log p_{t}(\mathbf{x}(t))+\nabla_{\mathbf{x}} \log p(\mathbf{y} \mid \mathbf{x}(t))
$$
因此我们要知道的仅仅是第二项。第一项生成模型已经解决了。但是第二项其实也不好算。对于一般的逆问题，作者考虑的推导是这样的：
$$
\nabla_{\mathbf{x}} \log p_{t}(\mathbf{x}(t) \mid \mathbf{y})=\nabla_{\mathbf{x}} \log \int p_{t}(\mathbf{x}(t) \mid \mathbf{y}(t), \mathbf{y}) p(\mathbf{y}(t) \mid \mathbf{y}) \mathrm{d} \mathbf{y}(t)
$$
$y_t$ 是用 $x_t$ 计算出来的

接着作者给了两个假设：

1. $p(\mathbf{y}(t) \mid \mathbf{y})$ 是可计算的。
2. $p_{t}(\mathbf{x}(t) \mid \mathbf{y}(t), \mathbf{y}) \approx p_{t}(\mathbf{x}(t) \mid \mathbf{y}(t))$ 成立。这个式子成立比较好理解，因为当 t 比较小的时候，$y_t$ 和 y 接近，当 t 比较大的时候 y 和 $x_t$ 没关系了。

最后就得到推导：
$$
\begin{aligned}
\nabla_{\mathbf{x}} \log p_{t}(\mathbf{x}(t) \mid \mathbf{y}) & \approx \nabla_{\mathbf{x}} \log \int p_{t}(\mathbf{x}(t) \mid \mathbf{y}(t)) p(\mathbf{y}(t) \mid \mathbf{y}) \mathrm{d} \mathbf{y} \\
& \approx \nabla_{\mathbf{x}} \log p_{t}(\mathbf{x}(t) \mid \hat{\mathbf{y}}(t)) \\
&=\nabla_{\mathbf{x}} \log p_{t}(\mathbf{x}(t))+\nabla_{\mathbf{x}} \log p_{t}(\hat{\mathbf{y}}(t) \mid \mathbf{x}(t)) \\
& \approx \mathbf{s}_{\boldsymbol{\theta}} *(\mathbf{x}(t), t)+\nabla_{\mathbf{x}} \log p_{t}(\hat{\mathbf{y}}(t) \mid \mathbf{x}(t))
\end{aligned}
$$
$\hat{y}(t)$ 是 $p(y(t)|t)$ 的一个采样。

我们把上述的方法用在不同的逆问题上。

对于类别条件生成，y_t 是恒定的。所以作者直接考虑训练了一个分类网络 $p_{t}(\mathbf{y} \mid \mathbf{x}(t))$，这样就直接得到 (23) 的第二项。

对于 inpaiting 问题，推导更复杂了。因为在 inpainting 问题上要difussion 的只有被挖去的区域。所以 SDE 可以直接定义在被挖去的区域上，这部分记为 $z_t$，推导的结果是：
$$
\begin{aligned}
\mathrm{d} \mathbf{z}=\left\{\mathbf{f}_{\bar{\Omega}}(\mathbf{z}, t)-\nabla \cdot\left[\mathbf{G}_{\bar{\Omega}}(\mathbf{z}, t) \mathbf{G}_{\bar{\Omega}}(\mathbf{z}, t)^{\top}\right]\right.\\
&\left.-\mathbf{G}_{\bar{\Omega}}(\mathbf{z}, t) \mathbf{G}_{\bar{\Omega}}(\mathbf{z}, t)^{\top} \nabla_{\mathbf{z}} \log p_{t}(\mathbf{z} \mid \Omega(\mathbf{z}(0))=\mathbf{y})\right\} \mathrm{d} t+\mathbf{G}_{\bar{\Omega}}(\mathbf{z}, t) \mathrm{d} \overline{\mathbf{w}}
\end{aligned}
$$
核心是 score-based model。作者的估计是：
$$
\begin{aligned}
\nabla_{\mathbf{z}} \log p_{t}(\mathbf{z}(t) \mid \Omega(\mathbf{x}(0))=\mathbf{y}) & \approx \nabla_{\mathbf{z}} \log p_{t}(\mathbf{z}(t) \mid \hat{\Omega}(\mathbf{x}(t))) \\
&=\nabla_{\mathbf{z}} \log p_{t}([\mathbf{z}(t) ; \hat{\Omega}(\mathbf{x}(t))])
\end{aligned}
$$
这里的 $[\mathbf{z}(t) ; \hat{\Omega}(\mathbf{x}(t))]$ 其实是一个图像，z_t 是 z 部分的 diffusion，剩下来的则是已知部分的 diffusion，这个可以直接算出来。所以计算的梯度，只要给出 $z$ 部分就可以了。这个和上面的一般情形的推导是相关的。因为只有 z 是不确定的，所以可以把 x 换成 z，然后对应 $x_t$ 变成 z 和 $\hat{\Omega}(\mathbf{x}(t))$ 的组合。第二项因为 y_t 就是 $\hat{\Omega}(\mathbf{x}(t))$，和 z 没有关系，所以对 z 求导都是 0.



## 反思

从 (16) 式，SDE 到 ODE 的关联，是否可以这样理解 flow 模型和 score-based 模型：score-based 模型虽然每一步都有 sample，但是总的来说 $p_t(x)$ 和对应的确定性 flow 的分布是一样的。而 SDE 模型的变化过程其实是固定的，也就是说 $p_t(x)$ 是固定的。因此确定性 flow 的 $p_t(x)$ 也是固定。但是一般的 flow 模型，中间过程是不确定的，需要用训练来实现。SDE 因为是固定的，所以对应 flow 模型中的变换也是固定的，这个从 (16) 中就可以看出来，只不过这个固定的变换需要知道 $\nabla_{\mathbf{x}} \log p_{t}(\mathbf{x})$ 也就是说，当 diffusion 过程给定的时候，整个概率变换就决定了，并且取决于 $\nabla_{\mathbf{x}} \log p_{t}(\mathbf{x})$，而这一项用 SDE 的演化可以训练出来的话，那么就知道实际的概率变换了。因此 score-based model 可以看成是知道每一步 x 概率变换的 sampling 或者条件分布，但是不知道对应的概率变换函数，所以用 score-based model 来拟合出 $\nabla_{\mathbf{x}} \log p_{t}(\mathbf{x})$，由此得到概率变换函数。或者说 score-based model 是知道概率变换函数形式，只是不知道具体取值。而一般的 flow 模型则是不知道使用什么概率变换形式是好的，只能通过极大似然概率来训练网络促使最终变换结果是给定的先验分布，然而这一点却无法保证。score-based model 则保证了终点的状态。如果单纯用 flow 模型的视角，就是固定了初始分布和最终分布以及中间过程分布，然后用 score-match 的方式获得中间过程分布的具体取值。这是和一般的 flow 模型本质不同的。score-based model 的核心就在于 $\nabla_{\mathbf{x}} \log p_{t}(\mathbf{x})$ 估计得好不好。

有没有什么办法可以融合 score-based 方法呢？一般 flow 模型的问题在于如何固定最终的分布。能不能引入 diffusion 的结构？或者引入 score-matching 的方法到训练的过程中保证训练效果呢？



## 参考文献

Yang Song and Stefano Ermon. Generative modeling by estimating gradients of the data distribution. In Advances in Neural Information Processing Systems, pp. 11895–11907, 2019.

Jascha Sohl-Dickstein, Eric Weiss, Niru Maheswaranathan, and Surya Ganguli. Deep unsupervised learning using nonequilibrium thermodynamics. In International Conference on Machine Learning, pp. 2256–2265, 2015.

Jonathan Ho, Ajay Jain, and Pieter Abbeel. Denoising diffusion probabilistic models. Advances in Neural Information Processing Systems, 33, 2020.

Florian Bordes, Sina Honari, and Pascal Vincent. Learning to generate samples from noise through infusion training. arXiv preprint arXiv:1703.06975, 2017.

Anirudh Goyal Alias Parth Goyal, Nan Rosemary Ke, Surya Ganguli, and Yoshua Bengio. Variational walkback: Learning a transition operator as a stochastic recurrent net. In Advances in Neural Information Processing Systems, pp. 4392–4402, 2017.

Yilun Du and Igor Mordatch. Implicit generation and modeling with energy based models. In H. Wallach, H. Larochelle, A. Beygelzimer, F. d'Alch´e-Buc, E. Fox, and R. Garnett (eds.), Advances in Neural Information Processing Systems, volume 32, pp. 3608–3618. Curran Associates, Inc., 2019.

Yang Song and Stefano Ermon. Improved techniques for training score-based generative models. Advances in Neural Information Processing Systems, 33, 2020.

Yang Song, Sahaj Garg, Jiaxin Shi, and Stefano Ermon. Sliced score matching: A scalable approach to density and score estimation. In Proceedings of the Thirty-Fifth Conference on Uncertainty in Artiﬁcial Intelligence, UAI 2019, Tel Aviv, Israel, July 22-25, 2019, pp. 204, 2019a.

Tianyu Pang, Kun Xu, Chongxuan Li, Yang Song, Stefano Ermon, and Jun Zhu. Efﬁcient learning of generative models via ﬁnite-difference score matching. arXiv preprint arXiv:2007.03317, 2020.