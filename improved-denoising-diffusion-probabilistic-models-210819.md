

# Improved Denoising Diffusion Probabilistic Models

[TOC]

## 信息

Authors: Alex Nichol and Prafulla Dhariwal

Year: arXiv 2021

Bibtex:

```latex
@article{nichol2021improved,
  title={Improved denoising diffusion probabilistic models},
  author={Nichol, Alex and Dhariwal, Prafulla},
  journal={arXiv preprint arXiv:2102.09672},
  year={2021}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/improved-denoising-diffusion-probabilistic-models.pdf)

code: https://github.com/openai/improved-diffusion



## 概括

这篇文章针对 DDPM 模型提出了一些改进。这篇文章的主要内容有两个，一个是如何改进似然概率的估计结果，一个是如何快速采样。

改进似然概率主要从损失函数和噪声水平 schedule 设计入手，作者提出了一种可学习协方差的方式。快速采样则是比较简单的从给定的 schedule 中计算相关参数。



## 方法

作者先介绍了 DDPM 模型。然后指出 DDPM 模型的一个问题是似然概率的估计表现比较差。作者首先发现增加扩散 step 的数量就可以提高似然概率的估计。所以这篇文章，作者使用了 T = 4000.

这篇文章的主要内容有两个，一个是如何改进似然概率的估计结果，一个是如何快速采样。

### Improving the log-likelihood

#### 第一个改进是协方差的改进

作者分析了原始 DDPM 模型中，为什么 $\sigma_t$ 选择两种不同的 $\beta$ 效果都差不多，这是因为两个 $\beta$ 值实际上相差非常小，特别是当扩散 step 数量非常大的时候。

![屏幕快照 2021-08-19 下午6.04.31](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-19 下午6.04.31.png)

所以作者认为对于 sample 质量，起主要作用的是 $\mu$，而不是方差。但是作者认为方差可能会影响似然概率。原本的 DDPM 模型，使用的固定的值，这篇文章的作者则提出了在两个 $\beta$ 值之间进行差值：
$$
\Sigma_{\theta}\left(x_{t}, t\right)=\exp \left(v \log \beta_{t}+(1-v) \log \tilde{\beta}_{t}\right)
$$
注意，这里的 v 是向量，因此这个协方差是针对每个维度的方差。

作者并没有限制 v 的范围，理论上可能会产生差值以外的值，但是实际的结果显示，不会产生这种情况。由于协方差由 v 产生，而 v 是网络的预测值（实际上也是一个类似 image 的 output），所以如果只使用原来的 score-based 损失函数是不行的，还需要考虑协方差的损失函数。因此作者提出的损失函数是一个混合损失函数。
$$
L_{\text {hybrid }}=L_{\text {simple }}+\lambda L_{\mathrm{vlb}}
$$
$\lambda = 0.001$，同时还使用 stop-gradient 训练技巧（不太懂），这样使得主要的训练还是原来的 L_simple。实际上 $L_{vlb}$ 就是原本的完整的损失函数。

作者后面的实验发现，要想让似然概率的效果最好，要使用 $L_{vlb}$ 作为唯一的损失函数，但是这样训练的网络会比较不稳定，主要原因是因为训练的时候梯度的噪声较大。为了解决这个问题，作者考虑对不同 t 的 L，增加权重。
$$
L_{\mathrm{vlb}}=E_{t \sim p_{t}}\left[\frac{L_{t}}{p_{t}}\right], \text { where } p_{t} \propto \sqrt{E\left[L_{t}^{2}\right]} \text { and } \sum p_{t}=1
$$
这个 $p_t$ 实际上是通过训练过程中 $L_t$ 的值来估计大致的大小，是一个动态变动的权重。至于这里的 t 采样的概率，不太确定是不是真的按照当前估计的 p 来抽样，我自己认为增加权重就可以了。（这个内容在论文中是属于第三个改进）。

如果只使用 $L_{vlb}$，生成质量会差一些，所以作者最后使用的是混合损失函数，好处是生成质量基本不会变差，而且似然函数估计好很多（我感觉可以参考 song yang 的 maximum 损失函数那篇文章的新的做法，不仅计算简单，而且似然函数效果也不错）

#### 第二个改进是噪声水平的变化

原本 DDPM 的做法是线性变动 $\beta_t$，这导致噪声过快地加到图像上，整个图像的变换有很大一部分是噪声。作者提出了一种新的 $\beta_t$ 的 schedule，使得似然估计效果更好。生成质量在 ImageNet 64 上的效果更好，而 CIFAR-10 差一些。

![屏幕快照 2021-08-19 下午6.04.41](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-19 下午6.04.41.png)

简单来说，首先根据下面的公式确定 $\bar{\alpha_t}$
$$
\bar{\alpha}_{t}=\frac{f(t)}{f(0)}, \quad f(t)=\cos \left(\frac{t / T+s}{1+s} \cdot \frac{\pi}{2}\right)^{2}
$$
然后用 $\beta_{t}=1-\frac{\bar{\alpha}_{t}}{\bar{\alpha}_{t-1}}$ 计算 $\beta_t$. 这里的 s 是为了控制 $\beta_0$ 不要太小。作者给出的结果是 $s = 0.008$，这样计算的 $\beta_0$ 差不多就比 pixel 的最小差异值小一点。使用 $\cos^2$ 的原因是这个函数更符合作者预期的变化过程。

另一方面，作者还控制 $\beta$ 不会大于 $0.999$，不过我好奇为什么会出现这么大的数。我觉得 $\beta$ 太大是不好的，因为这对应着均值的变化太大了，我觉得这会导致估计不准确。我认为可以考虑像 score-based 的改进，用一些简单的假设计算噪声分布，也许不需要训练如此的噪声，或者说噪声分布的变化过程可以不是 DDPM 的变化过程。不知道估计的准确性是否和扩散模型的设置是有关的。

### Faster sampling

作者发现用混合损失函数的模型可以让 sampling 的步数更少的情况下，生成图像质量也很不错。

作者的想法，我们先给出一个时间 t 的子序列 S，然后根据子序列对应的 $\bar{\alpha}_{S_t}$ 计算这个子序列的 $\beta$。
$$
\beta_{S_{t}}=1-\frac{\bar{\alpha}_{S_{t}}}{\bar{\alpha}_{S_{t-1}}}, \quad \tilde{\beta}_{S_{t}}=\frac{1-\bar{\alpha}_{S_{t-1}}}{1-\bar{\alpha}_{S_{t}}} \beta_{S_{t}}
$$
$p\left(x_{S_{t-1}} \mid x_{S_{t}}\right)$ 的分布就可以算出来了。

因为子序列长度小，所以采样时间就快了。实际上作者用的子序列就是均匀地从 0 ～ T 选择（如果不是整数值就取附近最接近的那个时刻 t）

因为模型估计的差值的权重 v，所以在这种情况下，可以算出对应的协方差矩阵。注意这里的均值是可以算的，因为网络估计的是噪声。$\mu_{\theta}\left(x_{t}, t\right)=\frac{1}{\sqrt{\alpha_{t}}}\left(x_{t}-\frac{\beta_{t}}{\sqrt{1-\bar{\alpha}_{t}}} \epsilon_{\theta}\left(x_{t}, t\right)\right)$

> 我总感觉 DDPM 的损失函数是可以改进的。我们需要从朗之万动态采样的角度和训练的意义角度来思考 DDPM 的损失函数的意义和 inference 的过程。

另外，song yang 等人提出的 DDIM 也是一种让 sampling 次数更少的策略。需要看一下。

实验表明，即使采样次数少了很多，效果也很不错。

## 实验

这篇文章的实验主要是作者提出的几个改进的效果。

使用更快速的 sampling 方法的生成效果。

作者和 gan 进行了比较。

考虑了不同大小模型的生成效果。

另外这篇文章还提出了类似于 SR3 的做法，这应该是 SR3 的前身。SR3 只不过是做了更多的实验。



## 参考文献

Jolicoeur-Martineau, A., Pich´e-Taillefer, R., des Combes, R. T., and Mitliagkas, I. Adversarial score matching and improved sampling for image generation, 2020. 这篇也是 score based model，以后写参考文献有用。

Chen, N., Zhang, Y., Zen, H., Weiss, R. J., Norouzi, M., and Chan, W. Wavegrad: Estimating gradients for waveform generation, 2020b. 这篇文章虽然讲的是语音生成，但是提到了加快 sampling 的技巧。并且在后来的扩散模型中在图像上也应用了。

Song, J., Meng, C., and Ermon, S. Denoising diffusion implicit models, 2020a. 这篇文章给出了另一种加快 sampling 的方法

Kynk¨a¨anniemi, T., Karras, T., Laine, S., Lehtinen, J., and Aila, T. Improved precision and recall metric for assessing generative models, 2019. 另一种生成质量评价指标。

Parmar, N., Vaswani, A., Uszkoreit, J., Kaiser, Ł., Shazeer, N., Ku, A., and Tran, D. Image transformer. arXiv preprint arXiv:1802.05751, 2018.

Child, R., Gray, S., Radford, A., and Sutskever, I. Generating long sequences with sparse transformers, 2019.

Roy, A., Saffar, M., Vaswani, A., and Grangier, D. Efﬁcient content-based sparse attention with routing transformers, 2020.

这三篇是 transformer 方法。但是可能不能用于生成高分辨率图像。

Gao, R., Song, Y., Poole, B., Wu, Y. N., and Kingma, D. P. Learning energy-based models by diffusion recovery likelihood, 2020. 这篇文章是扩散过程和能量模型的结合？



## 更多细节

### 网络结构

基本和 DDPM 一样，但是有一些改进。

1. changed the attention layers to use multi-head attention
2. use four attention heads
3. employ attention not only at the 16x16 resolution, but also at the 8x8 resolution.
4. changed the way the model conditions on t. In particular, instead of computing a conditioning vector v and injecting it into hidden state h as GroupNorm(h + v), we compute conditioning vectors w and b and inject them into the hidden state as GroupNorm(h)(w + 1) + b

网络结构设置：

ImageNet 64 × 64：

1. The downsampling stack performs four steps of downsampling, each with three residual blocks
2. The upsampling stack is setup as a mirror image of the downsampling stack
3. From highest to lowest resolution, the UNet stages use [C, 2C, 3C, 4C] channels, respectively.
4. C = 128
5. our model is comprised of 120M parameters

CIFAR-10 experiments：

1. a smaller model with three resblocks per downsampling stage and layer widths [C, 2C, 2C, 2C] with C = 128.
2. dropout = 0.3

### 损失函数

$$
L_{\text {hybrid }}=L_{\text {simple }}+\lambda L_{\mathrm{vlb}}, \lambda=0.001
$$

### 数据预处理

无

### 训练参数设定

对于 C = 128 的网络，学习率是 1e-4，其他的模型则是相对地乘上 $1 / \sqrt{\text { channel multiplier }}$

作者在 LSUN 的 fast sampling 的训练中，DDIM 方法用 batch=64，lr=2e-5 的效果是最好的。

### inference参数设定

无

### 实验结果

见论文。这篇文章的效果不错。

### 代码

https://github.com/openai/improved-diffusion

