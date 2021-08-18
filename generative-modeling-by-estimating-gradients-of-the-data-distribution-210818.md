# Generative Modeling by Estimating Gradients of the Data Distribution

[TOC]

## 信息

Authors: Yang Song and Stefano Ermon

Year: NeurIPS 2019

bibtex:

```latex
@article{song2019generative,
  title={Generative modeling by estimating gradients of the data distribution},
  author={Song, Yang and Ermon, Stefano},
  journal={arXiv preprint arXiv:1907.05600},
  year={2019}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/generative-modeling-by-estimating-gradients-of-the-data-distribution.pdf)

code: https://github.com/ermongroup/ncsn

IS and FID 计算：

https://github.com/openai/improved-gan/tree/master/inception_score

https://github.com/bioinf-jku/TTUR



## 概括

这篇文章提出了一个新的生成模型，通过朗之王动态采样的方法实现图像的采样生成。由于数据分布的梯度的估计是一个病态问题，为了克服这些困难，作者提出使用不同的高斯分布作为分布的扰动。用 score matching 的方法进行梯度的估计，然后通过退火形式的朗之万动态实现采样。作者提出的这种方法不需要使用对抗训练，生成的样本可以和 GAN 相比。同时在 CIFAR-10 上取得了非常好的 IS 的效果。另外，作者还利用这个模型解决 inpaiting 的任务。



## 方法

### 朗之万动态采样

朗之万动态采样是一种从概率分布 $p(x)$ 中生成样本的方法，并且仅仅需要知道 $\nabla_x\log p(x)$（为了简单起见，我们称其为对数概率梯度，并且忽略 $p(x)$，用其他的表述来指明具体的分布是什么）

给定一个固定的步长 $\epsilon > 0$，初值 $\tilde{x}_0$ 采样自分布 $\pi(x)$，朗之万动态计算如下：
$$
\tilde{\mathbf{x}}_{t}=\tilde{\mathbf{x}}_{t-1}+\frac{\epsilon}{2} \nabla_{\mathbf{x}} \log p\left(\tilde{\mathbf{x}}_{t-1}\right)+\sqrt{\epsilon} \mathbf{z}_{t}
$$
这里的 $z_t \sim N(0, I)$。理论上，当 $\epsilon \rightarrow 0, T \rightarrow \infin$ 时，$\tilde{x}_T$ 的分布就是 $p(x)$。

当 $\epsilon > 0, T>0$ 时，Metropolis-Hastings 更新是必要的，用来修正。不过在实践中，这个更新经常被忽略。这这篇文章中，这个更新也被忽略了，因为作者假设 $\epsilon$ 很小，同时 $T$ 足够大。

朗之万动态的核心是对数概率梯度，因此需要对其进行估计。

### 对数概率梯度的估计方法

对数概率梯度也称为 score function，作者在这篇文章给出了两种估计的方法。

通常情况下，如果模型 $s_{\theta}(x)$ 是用于对数概率梯度的估计，那么，目标函数应该是一个回归损失函数
$$
\frac{1}{2} \mathbb{E}_{p_{\text {data }}}\left[\left\|\mathbf{s}_{\boldsymbol{\theta}}(\mathbf{x})-\nabla_{\mathbf{x}} \log p_{\text {data }}(\mathbf{x})\right\|_{2}^{2}\right]
$$
已经被证明，上述的目标函数在某些正则条件下等价于
$$
\mathbb{E}_{p_{\text {data }}(\mathbf{x})}\left[\operatorname{tr}\left(\nabla_{\mathbf{x}} \mathbf{s}_{\boldsymbol{\theta}}(\mathbf{x})\right)+\frac{1}{2}\left\|\mathbf{s}_{\boldsymbol{\theta}}(\mathbf{x})\right\|_{2}^{2}\right]
$$
这里的 $\nabla_{\mathbf{x}} \mathbf{s}_{\boldsymbol{\theta}}(\mathbf{x})$ 是 $s_{\theta}(x)$ 的 Jacobian 矩阵。但是这个 Trace 的计算仍然非常大。下面给出两种替代的方法。

#### denoising score matching

这种方法，实际上和 Trace 计算无关，采用的是另一种思路。假设 $x$ 增加了一个噪声扰动 $q_{\sigma}(\tilde{x}|x)$，$\tilde{x}$ 是噪声图像，那么噪声图像的分布是 $q_{\sigma}(\tilde{x}) = \int q_{\sigma}(\tilde{x}|x) p_{data}(x)\mathrm{d}x$。如果我们使用如下的损失函数来训练 $s_{\theta}(x)$ 模型：
$$
\frac{1}{2} \mathbb{E}_{q_{\sigma}(\tilde{\mathbf{x}} \mid \mathbf{x}) p_{\text {data }}(\mathbf{x})}\left[\left\|\mathbf{s}_{\boldsymbol{\theta}}(\tilde{\mathbf{x}})-\nabla_{\tilde{\mathbf{x}}} \log q_{\sigma}(\tilde{\mathbf{x}} \mid \mathbf{x})\right\|_{2}^{2}\right]
$$
则当这个损失函数被极小化时，这个模型估计的就是 $q_{\sigma}$ 的对数概率梯度。也就是说，上述损失函数等价于直接估计 $q_{\sigma}$ 的对数概率梯度。当噪声非常小的时候，$q_{\sigma}$ 近似于 $p_{data}$，因此噪声图像的对数概率梯度近似于我们的目标分布的对数概率梯度。

#### sliced score matching

这种方法则是使用随机投影的方法来估计 Trace，因此公式 (3) 的目标函数改为：
$$
\mathbb{E}_{p_{\mathbf{v}}} \mathbb{E}_{p_{\text {data }}}\left[\mathbf{v}^{\top} \nabla_{\mathbf{x}} \mathbf{s}_{\boldsymbol{\theta}}(\mathbf{x}) \mathbf{v}+\frac{1}{2}\left\|\mathbf{s}_{\boldsymbol{\theta}}(\mathbf{x})\right\|_{2}^{2}\right]
$$
比如，当 $v$ 是一个多元标准高斯分布。$\mathbf{v}^{\top} \nabla_{\mathbf{x}} \mathbf{s}_{\boldsymbol{\theta}}(\mathbf{x}) \mathbf{v}$ 可以通过自动微分机制更快速的进行计算。不过这种方法的计算量比 denoising score matching 多4倍，也很费时。

因此在这篇文章中，作者使用 denoising score matching 方法。理论上，只要估计出 score function，之后使用朗之万动态采样就可以了。但是实践中仍然存在问题。

### score-based 生成模型的挑战

作者在文章中给出了两个挑战。

#### 流形假设的挑战

这个假设的含义是说图像是低维的，这导致两个问题：1. 图像空间很多地方是没有对数概率的（因为概率就是 0），2. score function 估计有效的前提条件是数据分布的支撑集是整个空间。作者通过一个 ResNet 来训练 score function，用 sliced score matching，发现直接用图像数据集 CIFAR-10 训练非常不稳定，无法得到好的结果，但是如果对图像增加一个微小的噪声，训练结果就会正常很多。参见附录 B.1

![屏幕快照 2021-08-18 上午11.19.28](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-18 上午11.19.28.png)

#### 低概率区域的挑战

由于低概率区域估计的缺失，这会导致 MCMC 朗之万动态采样的时候会出问题。

第一个例子是不正确的 score estimation。比如，作者设计了一个简单的二维混合高斯分布，这个分布里，两个 mode 相距甚远，几乎可以认为中间的区域概率是 0，或者说两个概率密度高的区域不相交。通过训练，score function 和真实的对数概率梯度相差很大。

![屏幕快照 2021-08-18 上午11.23.47](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-18 上午11.23.47.png)

这个结果表明只有在高概率的区域，估计是比较准确的。参见附录 B.1

第二个例子是朗之万动态采样时的问题。仍然是前述的混合高斯分布，使用真实的对数梯度概率，由于这个对数概率梯度和混合高斯模型 $p_{\text {data }}(\mathbf{x})=\pi p_{1}(\mathbf{x})+(1-\pi) p_{2}(\mathbf{x})$ 的参数 $\pi$ 无关，导致朗之万动态采样的时候无视 $\pi$，这样生成的结果是不好的。这个问题在多 mode 分布，且相交区域的概率密度很小的情况也会发生。而作者后来提出的退火方法则解决了这个问题。

### NCSN 模型

为了解决上述的问题，作者提出改进 score-based 生成模型的方法是对数据进行不同程度扰动，用一个网络来估计所有扰动后的对数概率梯度，同时朗之万动态采样的时候，从高噪声的分布开始采样，逐步退火为低噪声的采样。

设 $\sigma_i$ 满足 $\sigma_{i}/\sigma_{i+1}=C > 1$，那么可以定义加噪声的过程是 $\tilde{x}|x \sim N(x, \sigma^2I)$。通过设定使得 $\sigma_1$ 非常大，$\sigma_{L}$ 非常小。训练网络使得 $s_{\theta}(x, \sigma) \approx \nabla_x \log q_{\sigma}(x)$。

网络结构的选择是 U-Net 和空洞卷积的结合，使用了 instance normalization，而 $\sigma$ 是作为条件作用在 normalization 过程中。详细的内容参见最后的小节。参见附录 A

由于噪声水平有很多个，所以损失函数实际上是各个噪声下的损失函数，同时每个噪声对应不同的权重。
$$
\ell(\boldsymbol{\theta} ; \sigma) \triangleq \frac{1}{2} \mathbb{E}_{p_{\text {data }}(\mathbf{x})} \mathbb{E}_{\tilde{\mathbf{x}} \sim \mathcal{N}\left(\mathbf{x}, \sigma^{2} I\right)}\left[\left\|\mathbf{s}_{\boldsymbol{\theta}}(\tilde{\mathbf{x}}, \sigma)+\frac{\tilde{\mathbf{x}}-\mathbf{x}}{\sigma^{2}}\right\|_{2}^{2}\right]
$$
注意，$\nabla_{\tilde{\mathbf{x}}} \log q_{\sigma}(\tilde{\mathbf{x}} \mid \mathbf{x})=-(\tilde{\mathbf{x}}-\mathbf{x}) / \sigma^{2}$

这是每个 $\sigma$ 的噪声，合起来的则是：
$$
\mathcal{L}\left(\boldsymbol{\theta} ;\left\{\sigma_{i}\right\}_{i=1}^{L}\right) \triangleq \frac{1}{L} \sum_{i=1}^{L} \lambda\left(\sigma_{i}\right) \ell\left(\boldsymbol{\theta} ; \sigma_{i}\right)
$$
在这里，作者希望权重可以调节为每个 $\sigma$ 的值差不多，当网络训练较好的时候，$\left\|\mathbf{s}_{\boldsymbol{\theta}}(\mathbf{x}, \sigma)\right\|_{2} \propto 1 / \sigma$，因此，作者选择 $\lambda(\sigma) = \sigma^2$. 因此 $\lambda(\sigma) \ell(\boldsymbol{\theta} ; \sigma)=\sigma^{2} \ell(\boldsymbol{\theta} ; \sigma)=\frac{1}{2} \mathbb{E}\left[\left\|\sigma \mathbf{s}_{\boldsymbol{\theta}}(\tilde{\mathbf{x}}, \sigma)+\frac{\tilde{\mathbf{x}}-\mathbf{x}}{\sigma}\right\|_{2}^{2}\right.$

恰好，$\frac{\tilde{\mathbf{x}}-\mathbf{x}}{\sigma} \sim N(0, I)$。

### 退火朗之万动态的采样过程

![屏幕快照 2021-08-18 下午2.17.57](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-18 下午2.17.57.png)

这种采样过程的核心想法，当噪声水平高的时候，几乎不存在图像数据的种种问题，因此朗之万动态采样是比较有效的。此时采样出的结果可以作为下一个噪声水平较低的采样的初始值，这就避免了随机初始值导致的很多问题。

关于采样过程中的 $\alpha_i$ 的选择问题。作者的选择是 $\alpha_i \propto \sigma_i^2$，目的是让信噪比是一个固定的常数，和 $\sigma_i$ 无关。
$$
\left.\left\|\frac{\alpha_{i} \mathbf{s}_{\theta}\left(\mathbf{x}, \sigma_{i}\right)}{2 \sqrt{\alpha_{i}} \mathbf{z}}\right\|_{2}^{2}\right] \approx \mathbb{E}\left[\frac{\alpha_{i}\left\|\mathbf{s}_{\theta}\left(\mathbf{x}, \sigma_{i}\right)\right\|_{2}^{2}}{4}\right] \propto \frac{1}{4} \mathbb{E}\left[\left\|\sigma_{i} \mathbf{s}_{\boldsymbol{\theta}}\left(\mathbf{x}, \sigma_{i}\right)\right\|_{2}^{2}\right] \approx \frac{1}{4}
$$


## 实验

数据集使用了 MNIST，CelebA，CIFAR-10.

CelebA 的处理是先选择中心区域的 140x140，然后 resize 为 32x32

其他更多设定参见最后一个小节。

评价指标使用了 FID 和 IS，这两个指标只是用于 CIFAR10 数据集。其他两个则是因为大家很少用来别计较，而且CelebA 的处理可能都是不同的。

关于 inpainting 的实验，只是在朗之万动态的过程中，稍加改动。

![屏幕快照 2021-08-18 下午2.33.13](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-18 下午2.33.13.png)



## 更多细节

这个部分将会针对这篇文章的实验、网络、数据集处理等进行更全面、详细的叙述，以用于复现。

### 网络结构

网络结构部分分为三个主要部件。instance normalization, dilated convolutions and U-Net-type architectures，我们分别介绍。

#### Instance normalization

在这个改进的 IN 层中，scale 和 bias 是和 $\sigma$ 有关。每个 $\sigma$ 对应一个 scale 和 bias。因此，假设 i 表示 $\sigma$ 的下表，k 表示 feature 的下标，这个 conditional instance normalization 可以写成：
$$
\mathbf{z}_{k}=\gamma[i, k] \frac{\mathbf{x}_{k}-\mu_{k}}{s_{k}}+\beta[i, k]
$$
$\gamma \in \mathbb{R}^{L \times C} \text { and } \beta \in \mathbb{R}^{L \times C}$，从实现的角度来说，就可以看成是有 $L$ 个不同的 instance normalization 层.

作者表示，IN 层的一个缺点是没有考虑不同 $\mu_k$ 的信息，所以改进了一下。

首先计算 $\mu_k$ 的均值和方差，记为 $m, v$，然后用一个额外的参数 $\alpha$ 作为 scale 的系数。
$$
\mathbf{z}_{k}=\gamma[i, k] \frac{\mathbf{x}_{k}-\mu_{k}}{s_{k}}+\beta[i, k]+\alpha[i, k] \frac{\mu_{k}-m}{v}
$$
这相当于重新调整了一下 bias 的值。

这个 IN 层称为 CondInstanceNorm++。

总结：IN 层的改进有两个内容，一是增加 $\sigma$ 的条件，二是对 bias 增加了不同 feature 之间的关联。

#### Dilated convolutions

没有什么特别的说明。

#### U-Net architecture

使用的 U-Net 的变体，RefineNet（G. Lin, A. Milan, C. Shen, and I. Reid. Reﬁnenet: Multi-path reﬁnement networks for high-resolution semantic segmentation. In Proceedings of the IEEE conference on computer vision and pattern recognition, pages 1925–1934, 2017.）这个网络，作者没有详细说明，总的来说就是包含了 ResNet 的设计。

其他的一些说明如下：

1. RefineNet 是 4-cascaded（我猜测是说下采样四次之类的）
2. 使用 pre-activation residual blocks（这个是说激活函数放在最前面吗？）
3. 所有的 BN 层都替换为 CondInstanceNorm++（这个应该就是作者改进的 IN 层）
4. max pooling 层替换为 average pooling 层。（这个替换是为了让图像更平滑一些）
5. 在 Refine Block 中，CondINstanceNorm++ 在每个卷积层和 average pooling 层前面都加上了。但是原始版本的 Block 中是没有标准化层。（为什么要增加这么多？）
6. 激活函数选择了 ELU（似乎越来越多的激活函数使用了 ELU）
7. 使用空洞卷积替换 subsampling 层（除了第一个 residual block）。（空洞卷积是为了增加感受野，我个人认为这个可以替换成自注意力机制）
8. 每次计算下一个 cascade 时，空洞率增加 2 倍
9. 对于 CelebA 和 CIFAR-10，filters 的数量是第一个 cascade 128，后续加倍。对于 MNIST，则是第一个 64，然后加倍。



### 损失函数

参见如下的公式和设定。
$$
\ell(\boldsymbol{\theta} ; \sigma) \triangleq \frac{1}{2} \mathbb{E}_{p_{\text {data }}(\mathbf{x})} \mathbb{E}_{\tilde{\mathbf{x}} \sim \mathcal{N}\left(\mathbf{x}, \sigma^{2} I\right)}\left[\left\|\mathbf{s}_{\boldsymbol{\theta}}(\tilde{\mathbf{x}}, \sigma)+\frac{\tilde{\mathbf{x}}-\mathbf{x}}{\sigma^{2}}\right\|_{2}^{2}\right]
$$

$$
\mathcal{L}\left(\boldsymbol{\theta} ;\left\{\sigma_{i}\right\}_{i=1}^{L}\right) \triangleq \frac{1}{L} \sum_{i=1}^{L} \lambda\left(\sigma_{i}\right) \ell\left(\boldsymbol{\theta} ; \sigma_{i}\right)
$$

$$
\lambda(\sigma) = \sigma^2
$$



### 数据预处理

CelebA: 中心裁剪为 140x140，然后 resize 为 32x32

MNIST、CelebA、CIFAR-10 都 rescale 为 [0, 1] 的范围。

### 训练参数设定

L = 10，一共10种不同的噪声。

最大的噪声 $\sigma_1$ 为 1，最小的噪声 $\sigma_{10}$ 是 0.01

优化器：Adam，学习率 0.001，迭代次数 200000

batch size 固定为 128

训练时，CelebA 和 CIFAR-10 数据有翻转（但没说是水平还是垂直，这里应该是水平翻转）

每 5000 次迭代保存一次 checkpoint

MNIST：选择最后一个 checkpoint

CIFAR-10，CelebA：每个 checkpoint 生成 1000 张图片，选择 FID 最小的 checkpoint（但是 CelebA 怎么算 FID？）

GPU：MNIST 是一块 Titan XP，CelebA 和 CIFAR10 是两块 Titan XP

 

### inference参数设定

![屏幕快照 2021-08-18 下午2.17.57](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-18 下午2.17.57.png)

inference 的参数设定：

1. T = 100
2. $\epsilon = 2 \times 10^{-5}$，（实验表明 $5\times 10^{-6}$ 到 $5\times 10^{-5}$ 都可以）
3. 初始分布是均匀分布

### 实验结果

![屏幕快照 2021-08-18 下午3.16.34](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-18 下午3.16.34.png)

CIFAR-10数据集还计算了 IS 和 FID。使用了 50000 个 samples，计算结果是 8.87 和 25.32

![屏幕快照 2021-08-18 下午3.18.02](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-18 下午3.18.02.png)

### 代码

NCSN 的代码是: https://github.com/ermongroup/ncsn

计算 IS 的代码： https://github.com/openai/improved-gan/tree/master/inception_score

计算 FID的代码是：https://github.com/bioinf-jku/TTUR

