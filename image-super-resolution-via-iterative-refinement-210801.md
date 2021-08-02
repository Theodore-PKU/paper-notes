# Image Super-Resolution via Iterative Reﬁnement

[TOC]

## 信息

Authors: Chitwan Saharia, Jonathan Ho, William Chan, Tim Salimans, David J. Fleet, Mohammad Norouzi

Year: arXiv 2021

Google Research, Brain Team

Bibtex:

```latex
@article{saharia2021image,
  title={Image super-resolution via iterative refinement},
  author={Saharia, Chitwan and Ho, Jonathan and Chan, William and Salimans, Tim and Fleet, David J and Norouzi, Mohammad},
  journal={arXiv preprint arXiv:2104.07636},
  year={2021}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/image-super-resolution-via-iterative-refinement.pdf)



## 概括

这篇文章将 DDPM 方法应用于图像超分辨，称为 SR3，并且基于该模型进行了一些拓展。这篇文章的实验效果很不错。值得一提的是，这篇文章是从分布的角度来研究超分辨问题，因此有借鉴意义。



## 方法

### DDPM

这篇文章的基本思想和 DDPM 是一样的，但是由于是超分辨问题，因此和 DDPM 不一样的地方是，加入了低分辨率图像作为条件。我们首先回顾一下 DDPM 方法。

简单来说，DDPM 构造了一个从图像到高斯噪声的马尔可夫过程，并且推导出了一个逆过程，这个逆过程仍然是马尔可夫过程。DDPM 模型中，需要学习的则是逆过程中的一些参数。事实上，由于逆过程相当于多个高斯分布的迭代（实际上类似于朗之万动态），因此核心在于如何估计出这些高斯分布的均值。DDPM 的生成过程就是从高斯分布出发，一步一步迭代出图像。如果从 score-based model 的角度来看，就是一个逆 SDE 的某种离散，只不过这个离散比较复杂。更具体的共识参考 DDPM 原论文，之后我们仅仅考虑超分辨任务下的 DDPM 公式，并讨论它和原始的 DDPM 模型的差异。

### 超分辨下的 DDPM

DDPM 考虑的是图像分布 p(y) 和高斯分布之间的关系，超分辨下则是后验分布 p(y|x) 和高斯分布的关系。这里的 x 是低分辨率图像。

这篇文章也很清楚地表明，在超分辨任务下，实际的 y 应当是一个分布，通常的 MSE 回归任务仅仅是估计了均值。为了生成（估计）这个分布，作者使用了 DDPM。既然如此，可以直接理解为在给定 x 的情况，p(y|x) 和高斯分布的变换。这和图像分布 p(y) 和高斯分布的变化的区别在于，图像分布是将所有的 y 的高斯化（diffusion）过程都考虑进来，所以每个时间 t，分布是整个图像分布变动后的结果。但是在超分辨任务下，每个 x 都对应一个独立的图像分布 p(y|x)，这个条件分布各自对应一个高斯化 diffusion 过程。虽然这两个过程完全一致，但是 p(y) 在变化时，是以 $p_t(y_t) = \int p(y_t|y)p(y) dy$ 的形式来考虑的，超分辨则是 $p_t(y_t|x) = \int p(y_t|y, x)p(y|x)dy$. 实际上，我认为因为 x 是 y 的下采样，所以在不考虑 forward model 的误差的情况下，完全可以忽略 x，即 $y_t$ 和 y 之间的关系完全和原始 DDPM 模型中一样的。作者在论文中说，考虑 x 的 diffusion 过程在未来考虑。

在考虑了条件的情况下，diffusion 过程仅仅是 y|x 的高斯化，逆过程也是关于 x 的逆过程。 但由于 diffusion 过程，对于单个图像 y，仍然和原始 DDPM 一样，所以我们用原来的数学公式描述就可以了：
$$
\begin{aligned}
&q\left(\boldsymbol{y}_{1: T} \mid \boldsymbol{y}_{0}\right)=\prod_{t=1}^{T} q\left(\boldsymbol{y}_{t} \mid \boldsymbol{y}_{t-1}\right) \\
&q\left(\boldsymbol{y}_{t} \mid \boldsymbol{y}_{t-1}\right)=\mathcal{N}\left(\boldsymbol{y}_{t} \mid \sqrt{\alpha_{t}} \boldsymbol{y}_{t-1},\left(1-\alpha_{t}\right) \boldsymbol{I}\right)
\end{aligned}
$$
这个公式中，$\alpha$ 控制了 diffusion 的过程。一般来说，用 $\gamma_t = \prod^t_{i=1} \alpha_i$ 更为常见。这是因为我们可以得到 $y_t$ 关于初始 $y_0$ 的条件分布公式。
$$
q\left(\boldsymbol{y}_{t} \mid \boldsymbol{y}_{0}\right)=\mathcal{N}\left(\boldsymbol{y}_{t} \mid \sqrt{\gamma_{t}} \boldsymbol{y}_{0},\left(1-\gamma_{t}\right) \boldsymbol{I}\right)
$$
我认为逆过程的推导和原始 DDPM 必然是完全一样的，因为 p(y) 和 p(y|x) 本身没有任何区别。

所以逆过程应该是：
$$
\begin{aligned}
p_{\theta}\left(\boldsymbol{y}_{0: T} \mid \boldsymbol{x}\right) &=p\left(\boldsymbol{y}_{T}\right) \prod_{t=1}^{T} p_{\theta}\left(\boldsymbol{y}_{t-1} \mid \boldsymbol{y}_{t}, \boldsymbol{x}\right) \\
p\left(\boldsymbol{y}_{T}\right) &=\mathcal{N}\left(\boldsymbol{y}_{T} \mid \mathbf{0}, \boldsymbol{I}\right) \\
p_{\theta}\left(\boldsymbol{y}_{t-1} \mid \boldsymbol{y}_{t}, \boldsymbol{x}\right) &=\mathcal{N}\left(\boldsymbol{y}_{t-1} \mid \mu_{\theta}\left(\boldsymbol{x}, \boldsymbol{y}_{t}, \gamma_{t}\right), \sigma_{t}^{2} \boldsymbol{I}\right)
\end{aligned}
$$
很显然，通过上面的式子，我们可以得到一个迭代的过程，从高斯分布到 p(y|x) 的采样。$p(y_T)$ 由于是完全独立于 x 的，所以就不需要写 condition on x.

这里的核心其实如何算 $\mu_{\theta}$，因为这个值在逆过程的迭代中至关重要。作者在论文的 2.3 节中的推导我个人非常不喜欢，我认为不是很合理，最合理的逻辑要么是直接从 DDPM 的推导拓展而来，或者是利用 score-based model 的推论直接套用，而不是论文中的推导。但不管怎么说，这两个结果是一样的：
$$
\mu_{\theta}\left(\boldsymbol{x}, \boldsymbol{y}_{t}, \gamma_{t}\right)=\frac{1}{\sqrt{\alpha_{t}}}\left(\boldsymbol{y}_{t}-\frac{1-\alpha_{t}}{\sqrt{1-\gamma_{t}}} f_{\theta}\left(\boldsymbol{x}, \boldsymbol{y}_{t}, \gamma_{t}\right)\right)
$$
这里的 $f_\theta$ 是 DDPM 模型，也和 score function 密切相关。但是我们可以注意到，这里的输入包含了 x，因为 score function 是关于 $\nabla_{y_t} \log p(y_t|x)$ 的，所以 ，所以模型的输入必然包含 x。

这里先假设 f 已经得到了，那么逆过程就如下的算法所述：

![屏幕快照 2021-08-01 下午9.15.32](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-01 下午9.15.32.png)

这个算法完全和 DDPM 是一致的。唯一的区别就是模型中的输入多了 x。另外，

接下来的问题就是关于 $f_{\theta}$ 怎么训练的问题。

### 训练

由于 $f_{\theta}$ 表示的内容和 score function 几乎等价，和原始的 DDPM 也完全一样，所以很自然，损失函数也保持一致就可以了。
$$
\mathbb{E}_{(\boldsymbol{x}, \boldsymbol{y})} \mathbb{E}_{\boldsymbol{\epsilon}, \gamma}\left\|f_{\theta}(\boldsymbol{x}, \underbrace{\sqrt{\gamma} \boldsymbol{y}_{0}+\sqrt{1-\gamma} \boldsymbol{\epsilon}}, \gamma)-\boldsymbol{\epsilon}\right\|_{p}^{p},
$$
但这里，和 score function 略有区别的是，这里的 p 范数并不是 2 范数。这就和理论的 score function 是有区别的，因为理论的 score function 证明了这个损失函数在 2 范数的情况下，等价于 score function 的估计的损失函数。括弧包含的值就是和 $\gamma$ 相关的一个 $y_t$。这里的 gamma 表示的就是噪声水平了。epsilon 表示的和 $\nabla_{y_t} \log p(y_t|x)$ 相关。之所以可以这么做，其理论仍然是逆过程的条件分布 $p_{\theta}\left(\boldsymbol{y}_{t-1} \mid \boldsymbol{y}_{t}, \boldsymbol{x}\right) $ 是高斯的。

实际训练的时候，会对 gamma 进行随机采样，因为这个模型要对所有的 gamma 都有效。作者的做法是给定一个 gamma 的选取分布（分片分布 $p(\gamma)=\sum_{t=1}^{T} \frac{1}{T} U\left(\gamma_{t-1}, \gamma_{t}\right)$）。不过这里比较奇怪，因为如果 U 是均匀分布，而且 gamma 相邻值一样，那么就和直接均匀分布采样没啥区别了。唯一的可能是 gamma 之间不是相同的相邻值。但是如果是这样，怎么选取 gamma 也是一个问题。具体的训练方式可能和其他文章是相关的。

Nanxin Chen, Yu Zhang, Heiga Zen, Ron J. Weiss, Mohammad Norouzi, and William Chan. WaveGrad: Estimating Gradients for Waveform Generation. In ICLR, 2021.

### 生成

这里需要强调一下，训练的时候，gamma 的选择是非常多的，这并不意味着迭代也要迭代非常多步。实际上，作者参考了 wavegrad 的方法，只要迭代不超过 100 步就可以了。



### 网络结构

网络结构参考了 BigGAN，也是 Unet 结构，我感觉几乎和 song yang 的方法差不多。这里最主要的是 gamma 怎么用到 U-net 中。

而 x 是如何 condition 到网络中的呢？其实非常简单，作者将 x 进行上采样，达到和目标一样的分辨率，和 $y_t$ 一起合并，作为网络的输入。

一些关于 Unet 的细节，在附录 A 中。



## 实验

### 超分辨实验

人脸图像：16x16 到 128x128，64x64 到 512x512 的实验，训练用的是 FFHQ 数据，测试用的是 CelebA-HQ 数据。

自然图像：64x64 到 256x256，用的是 imagenet 数据。

更多的训练细节：

1. 对于 imagenet，处理手段是 we discard images where the shorter side is less than the target resolution. We use the largest central crop like [4], which is then resized to the target resolution using area resampling as our high resolution image.
2. 训练 1M 迭代（次），batch size 是 256，学习率用的是线性 warmup over 10k 迭代。然后用 1e-4 的学习率。
3. 网络参数各不相同，因为模型的通道数不一样。
4. dropout 也有区别。

详细的内容参论文 4 小节。

### 超分辨的比较和评估

比较对象有两类，一类是以 GAN 为基础的模型，一类是以回归为基础的模型（即 MSE 损失函数的模型，这个模型用和 SR3 一样的 U-Net 来训练，学习率改成了 1e-5）

评价指标方面，作者增加了人的评估。作者的理由是 PSNR，SSIM 这一类的指标是不合理的，因为 y|x 是分布，仅仅和 reference 图像进行比较是不对的。

至于和 GAN 模型的比较，则考虑了 FID 和 IS。GAN 模型作者考虑的是 PULSE[28]，FSRGAN[7]

为了说明 U-Net 回归模型的效果也很不错，作者用分类模型的准确率来评价（为啥不比较 PSNR 和 SSIM），比较对象是其他的超分辨回归模型（DRCN [22]，FSRCNN [13]，PsyCo [35]，ENet-E [44]， RCAN [64]）。不知道这些模型为啥要用这个指标。直接看 PSNR 不行吗？我感觉有问题。



### 拓展实验

拓展实验其实是先训练一个小的生成模型（不是超分辨，所以是 unconditional），用小的生成模型的图输入到超分辨模型中。如果要生成非常大的图，可以分别训练多个超分辨模型，每个超分辨模型分别放大一些（但是输入是不同的）。这样的好处是不用训练一次性放大倍数很大的模型。

作者发现，在这个实验中，With cascaded generation we found it effective to use more reﬁnement steps at low-resolutions, and fewer steps at higher resolutions.

在这个部分，作者也比较了其他的 GAN 生成模型，效果相当。



实验的很多图在附录 C 中。



## 讨论

We observed some evidence of mode dropping, the model consistently generates nearly the same image output during sampling (when conditioned on the same input). We also observed the model to generate very continuous skin texture in face super-resolution, dropping moles, pimples and piercings found in the reference.

这是作者观察到的现象，值得注意。



## 相关文献

待补充

Jonathan Ho, Ajay Jain, and Pieter Abbeel. Denoising diffusion probabilistic models. In NeurIPS, 2020.

Yang Song, Jascha Sohl-Dickstein, Diederik P. Kingma, Abhishek Kumar, Stefano Ermon, and Ben Poole. Score-Based Generative Modeling through Stochastic Differential Equations. In ICLR, 2021.

Nanxin Chen, Yu Zhang, Heiga Zen, Ron J. Weiss, Mohammad Norouzi, and William Chan. WaveGrad: Estimating Gradients for Waveform Generation. In ICLR, 2021.

