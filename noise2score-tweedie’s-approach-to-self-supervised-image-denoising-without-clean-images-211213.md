# Noise2Score: Tweedie’s Approach to Self-Supervised Image Denoising without Clean Images

[TOC]

## 信息

作者：Kwanyoung Kim, Jong Chul Ye

年份：2021

期刊平台：NeurIPS 2021

Bibtex:

```latex
@article{kim2021noise2score,
  title={Noise2Score: Tweedie's Approach to Self-Supervised Image Denoising without Clean Images},
  author={Kim, Kwanyoung and Ye, Jong Chul},
  journal={arXiv preprint arXiv:2106.07009},
  year={2021}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/noise2score-tweedie’s-approach-to-self-supervised-image-denoising-without-clean-images.pdf)



## 概括

精读。

这篇文章非常重要。作者研究的是自监督的去噪问题。作者自己提出的方法并没有特别的地方，因为主要是做了一个组合。但是作者能发现组合的作用，是非常重要的。简单来说，作者把去噪问题用 tweedie's formula 来求解，tweedie's formula 的核心是噪声图片的 score function，而这刚好是不需要 clean image 来训练的。至于 score function 的训练方法，作者使用 AR-DAE 的方法（不过作者的表述有一些问题），这个方法就是训练噪声图片 score function 的近似，和 score-based model 中的方法非常接近（几乎一样），只是应用不同。另外，作者还揭示了 SURE 和 score function 之间的关系，我认为这一点也非常重要。

这篇文章说是一个统一的框架，但实际上并不相同。和其他的自监督方法，思路有本质的差异。

虽然这篇文章没有给出代码，但是之后如果也是相关的工作，一定会和这篇文章进行比较。



## 方法

鉴于这篇文章对于 AR-DAE 和 DAE 的描述有些问题，所以我使用原始论文中的说法。另外，由于这篇文章的方法和 noise2noise 这一类的方法有较大差异（但论文说又关系），所以我们不讨论 noise2nosie 的方法。

在这篇文章中，去噪问题描述为：
$$
y = x + w
$$
$w$ 是噪声，但并没有指定噪声模型。如果是高斯噪声，作者考虑的是 iid 的高斯噪声 $\mathcal{N}(0, \sigma^2 I)$。

**Tweedie's formula**

tweedie's formula 分析了不同噪声模型下 $\mathbb{E}[x|y]$ 的计算方法。

假设条件分布 $p(y|\eta)$ 是一个指数族分布（这里用的是 $\eta$ 而不是 $x$ 是因为可能需要对 $x$ 进行变形）：
$$
p(y \mid \eta)=p_{0}(y) \exp \left(\eta^{\top} T(y)-\varphi(\eta)\right)
$$
利用贝叶斯公式，可以得到
$$
p(\eta \mid y)=\exp \left(\eta^{\top} T(y)-\lambda(y)\right)\left[p(\eta) e^{-\varphi(\eta)}\right], \quad \text { where } \quad \lambda(y)=\log \left(\frac{p(y)}{p_{0}(y)}\right)
$$
这说明，后验分布也是一个指数族分布。通过推导，当 $T(y) = y$ 时，可以得到后验分布的均值表达式：
$$
\hat{\eta}:=\mathbb{E}[\eta \mid y]=\lambda^{\prime}(y)=-l_{0}^{\prime}(y)+l^{\prime}(y)
$$
其中 $l^{\prime}(y):=\nabla_{y} \log p(y), l_{0}^{\prime}(y):=\nabla_{y} \log p_{0}(y)$

作者根据这个推导，得出了当噪声模型是高斯分布、gamma分布和泊松分布、指数分布、伯努利分布时，后验概率均值的计算。

![屏幕快照 2021-12-13 下午3.16.05](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-12-13 下午3.16.05.png)

从上表中，其实可以得到其他的一些噪声模型下的后验分布的均值的计算，其中核心的部分都是 $l^{\prime}(y):=\nabla_{y} \log p(y)$，而这正是 score function 的估计对象。注意，这里的 $\nabla_{y} \log p(y)$，是噪声图像的对数概率分布的梯度，和真实图像分布无关。这也正是 Teedie's formula 的强大之处。从理论上指明了自监督方法的有效性。

我们接下来仅仅考虑高斯分布的情形（也是最简单的情形）。
$$
\mathbb{E}[x \mid y]=y+\sigma^{2} \nabla_{y} \log p(y)
$$
在这个式子中，我们只要估计出 score-function 就可以了。这篇文章采用的方法是 AR-DAE，来源于 DAE 的方法。

**score function 的估计**

DAE 的方法可以描述如下：

给定 $\sigma_a > 0$，训练下面的损失函数，
$$
\ell_{D A E}(\Theta)=\underset{y \sim P_{Y} \atop u \sim \mathcal{N}(0, I)}{\mathbb{E}}\left\|y-F_{\Theta}\left(y+\sigma_{a} u\right)\right\|^{2}
$$
得到的最优 $F_{\Theta^*}(\cdot)$ 满足：
$$
F_{\Theta *}(y)=y+\sigma_{a}^{2} \nabla_y \log p_{Y}(y) +o\left(\sigma_{a}^{2}\right)
$$
实际上，我们可以用定理一和 Tweedie's formula 来理解。

假设 $y + \sigma_a u$ 构成了另外一个随机变量 $z$，那么根据定理一，我们知道 (3) 式优化的结果是：
$$
F_{\Theta^{*}}(z) = \mathbb{E}[y | z]
$$
利用 Tweedie's formula，（因为 $\sigma_a u$ 是一个高斯分布）可以推出：
$$
\mathbb{E}[y | z] = z + \sigma_a^2 \nabla_z \log p_Z(z)
$$
因此，从 (5), (6) 两式得到：
$$
\begin{align}
F_{\Theta^{*}}(z) &= z + \sigma_a^2 \nabla_z \log p_Z(z) \notag \\
& = z + \sigma_a^2 \nabla_z \log p_Y(z) + \sigma_a^2 \left(\nabla_z \log p_Z(z) - \nabla_z \log p_Y(z) \right)
\end{align}
$$
实际上 (4) 的提出者（Benjo）是证明了 $\nabla_z \log p_Z(z) - \nabla_z \log p_Y(z)$ 当 $\sigma_a$ 趋近于 0 的时候趋近于 0. (当然，这个似乎是一个非常显然的结论)

不过，从理论上，我们并不想要 (6) 的表达式，因为我们想知道的是 $y$ 的 score function，而不是 $z$ 的 score function，这是两个完全不同的分布（当 $\sigma_a \neq 0$ 的时候）。但是 (4) (7) 又告诉我们 $\sigma_a$ 很小的时候，可以看成是一种近似。于是：
$$
\nabla_y \log p_{Y}(y) \approx \frac{F_{\Theta *}(y) - y}{\sigma_a^2}
$$
以上是 DAE 的部分。

AR-DAE 则是在此基础上稍加改进。AR-DAE 的作者认为，为了得到好的估计，我们就得选择 $\sigma_a$ 非常小，但是这会导致 (8) 在数值计算上的不稳定。因此他们提出了另外一种训练的方式。

给定 $\sigma_a$ 时，令 $F_{\Theta}(z; \sigma_a) = \sigma_a^2 R_{\Theta}(z; \sigma_a) + z $，为什么要这么做？作者像直接用 $R_{\Theta}$ 来估计 $\nabla_z \log p_Z(z)$，那么原来的 $\ell_{DAE}$ 就变成：
$$
\begin{align}
\ell_{D A E}(\Theta)  &=\underset{y \sim P_{Y} \atop u \sim \mathcal{N}(0, I)}{\mathbb{E}}\left\|y-\sigma_a^2 R_{\Theta}(y+\sigma_{a} u; \sigma_a) + y+\sigma_{a} u \right\|^{2} \notag \\
& = \underset{y \sim P_{Y} \atop u \sim \mathcal{N}(0, I)}{\mathbb{E}}\left\|\sigma_{a} u -\sigma_a^2 R_{\Theta}(y+\sigma_{a} u; \sigma_a)  \right\|^{2} \notag \\
&= \sigma_{a}^2  \underset{y \sim P_{Y} \atop u \sim \mathcal{N}(0, I)}{\mathbb{E}}\left\|u -\sigma_a R_{\Theta}(y+\sigma_{a} u; \sigma_a)  \right\|^{2}
\end{align}
$$
忽略 $\sigma_a^2$，并且对不同的 $\sigma_a$ 都进行训练，$\sigma_a^2 \sim \mathcal{N}\left(0, \delta^2 \right)$，就构成了 AR-DAE 的训练。这实际上就是增加了一个权重 $\frac{1}{\sigma_a^2}$，最后表示成：
$$
\ell_{A R-D A E}(\Theta)=\underset{y \sim P_{Y}, u \sim \mathcal{N}(0, I) \atop \sigma_{a} \sim \mathcal{N}\left(0, \delta^{2}\right)}{\mathbb{E}}\left\|u+\sigma_{a} R_{\Theta}\left(y+\sigma_{a} u; \sigma_a \right)\right\|^{2} \\
$$
实际上 AR-DAE 的计算过程和 score-based 生成模型的训练本质上相同（形式上我感觉一模一样），只是，AR-DAE 最后只要考虑 $y$ 而不是 $y + \sigma_a u$ 的 score function，对于 score-based 生成模型来说，反而是后者更为重要。

更神奇的是，如果我们只考虑带权重的 (12) 式：
$$
\underset{y \sim P_{Y} \atop u \sim \mathcal{N}(0, I)}{\mathbb{E}}\left\|\frac{\sigma_a u}{\sigma_a^2} - R_{\Theta}(y+\sigma_{a} u; \sigma_a)  \right\|^{2}
$$
从 score-based 生成模型可知，这个式子就是 score-based 生成模型的损失函数。给定 $\sigma_a$ 的情况下，如果 $F_{\Theta^{*}}(z) = z + \sigma_a^2 \nabla_z \log p_Z(z)$，那么
$$
R_{\Theta^{*}}(z) = \nabla_z \log p_Z(z)
$$
也就是说我们直接用 Tweedie's formula 证明了 score-based 生成模型损失函数的有效性。

（捋一下逻辑链条，根据定理一和 Tweedie's formula，推出了 (10)，根据 (10) 推出了 (15)，所以 score-based 生成模型损失函数有效）

但是反过来也一样的。我们用定理一，本来是可以直接推出 score-based 生成模型损失函数有效，也就是 (15) 成立，那么也就推出了 (10) 是成立，在根据定理一，推出了 (8)，于是，可以推出 (9)，也就是推出了高斯分布情形下的 Tweedie's formula.

不过能否利用这个性质，从 Tweedie's formula 出发，来推导其他噪声模型下的扩撒模型的损失函数呢？因为扩散模型的核心是计算 score-function，Tweedie's formula 其实表明了它和 score function 直接存在联系。那么就可以利用定理一来构造出新的损失函数。

这里讨论了一些无关的内容。回到这篇论文。总而言之，使用 AR-DAE，可以得到不同 $\sigma_a$ 下的 $z$ 的 score function（注意不是 $y$），当 $\sigma_a$ 取 0 的时候，也许可以很好地泛化出 $y$ 的 score-function，也就是本文的目标核心。

不过这篇文章在上述的 DAE 和 AR-DAE 的描述中，有一些错误，我们的笔记进行了修正。总之作者采用的是 AR-DAE 的训练方法，训练出结果之后，我们不妨认为已经得到了 $y$ 的 score-function，此后就可以直接套用 Tweedie's formula 来计算 $\mathbb{E}[x|y]$ 了。这里需要注意的是，我们在计算 $y$ 的 score  function 的时候，在 $y$ 上施加了高斯噪声，但是这和 $y$ 与 $x$ 之间的噪声模型是独立的。

**关于 $\sigma$**

可以注意到，在上述的方法中，估计 score function 的时候，不需要知道噪声的参数，比如 $\sigma$ 或者其他分布的参数。在应用 Tweedie's formula 的时候才需要使用。这意味着，即使 $\sigma$ 是未知的，我们可以尝试使用不同的 $\sigma$，然后在某一个准则下，挑选一个最好的。比如，可以用 TV 作为图像质量的一个度量。

**和 SURE 方法的关联**

SURE 方法的核心是计算 SURE loss。最基本的 SURE 方法是针对高斯噪声的，如果是其他噪声，则需要使用更复杂的形式。我们这里仅仅考虑高斯噪声的 SURE 方法。通常，SURE loss 需要知道噪声的大小 $\sigma$。（完整的 SURE loss 不是下面的式子，这里忽略了一个常数）
$$
\ell_{S U R E}(\Theta)=\mathbb{E}_{y \sim P_{Y}}\left\{\left\|y-F_{\Theta}(y)\right\|^{2}+2 \sigma^{2} \operatorname{div}_{y} F_{\Theta}(y)\right\}
$$
但是在 SURE 的方法中，必须知道 $\sigma^2$ 才可以计算。这就导致 SURE 的方法无法在 $\sigma$ 未知的时候来训练，甚至连挑选 $\sigma$ 的可能性都不存在。

但是有趣的是，作者指出，如果我们仍然使用
$$
F_{\Theta}(z; \sigma_a) = \sigma_a^2 R_{\Theta}(z; \sigma_a) + z
$$
带入 SURE 的 loss function，就会得到
$$
\ell_{S U R E}(\Theta)= \mathbb{E}_{y \sim P_{Y}}\left\{\sigma^{4}\left\|R_{\Theta}(y)\right\|^{2}+2 \sigma^{4} \operatorname{div}_{y} R_{\Theta}(y)\right\}+2 \sigma^{2} \operatorname{dim}(y)
$$
忽略常数的部分，会不会觉得很熟悉？（这里换了记号，不是原来论文的记号）
$$
\frac{1}{2} \mathbb{E}_{p_{\text {data }}}\left[\left\| {\mathbf{s}_{\theta}}(\mathbf{x})-\nabla_{\mathbf{x}} \log p_{\text {data }}(\mathbf{x})\right\|_{2}^{2}\right] \Leftrightarrow
\mathbb{E}_{p_{\text {data }}(\mathbf{x})}\left[\operatorname{tr}\left(\nabla_{\mathbf{x}} \mathbf{s}_{\theta}(\mathbf{x})\right)+\frac{1}{2}\left\|\mathbf{s}_{\theta}(\mathbf{x})\right\|_{2}^{2}\right]
$$
这里的 $s_{\theta}$ 就是 score function，上述的两个损失函数是等价的。也就是说右边的式子可以用来估计 socre function，而
$$
\operatorname{tr}\left(\nabla_{\mathbf{x}} \mathbf{s}_{\theta}(\mathbf{x})\right) = \operatorname{div}_{\mathbf{x}} \mathbf{s}_{\theta}(\mathbf{x})
$$
(19) 式的右边就是 (18) 式去掉常数项的部分。因此 SURE 损失函数的本质就是在估计 score function，只不过形式上包含了 Tweedie's formula （请看 (17) 式）。我们可以这么理解，从 (19) 式出发，添加了一些常数 $\sigma$ 变成了 (18) 式，由于 $R_{\Theta}$ 就是 score function，根据 Tweedie's formula，可以推出 (17)，将 (17) 带入 (18)，就会变成 (16) 式，也就是 SURE 的原始 loss 形式。因此，我们训练 SURE 函数的时候，完全没有必要采用远来的形式，用 (19) + Tweedie's formula 就可以了。这样一来，就和 Noise2Score 的形式完全一样。只不过估计 score function 的方法不同，SURE 用的是 (19)，Noise2Score 用的是 AR-DAE. 因此某种程度上，Noise2Score 也可以看成是 SURE 的改进。

不过，实际上，(19) 式很难算，因为 div 的原因。通常会使用一个技巧，叫 slice score matching，这是一个很常见的方法：
$$
\mathbb{E}_{p_{\mathbf{v}}} \mathbb{E}_{p_{\text {data }}}\left[\mathbf{v}^{\top} \nabla_{\mathbf{x}} \mathbf{s}_{\boldsymbol{\theta}}(\mathbf{x}) \mathbf{v}+\frac{1}{2}\left\|\mathbf{s}_{\boldsymbol{\theta}}(\mathbf{x})\right\|_{2}^{2}\right]
$$
这个技巧在 SURE loss 的计算中也使用了。要求 $v$ 是一个0均值，协方差矩阵为 $\epsilon^2 I$ 的分布，高斯分布就可以。

作者在论文中，估计 score function 的时候，大家都不用 slice score matching 了，都用 DAE 的方法。所以他们认为 SURE 会收到 score function 估计不准确的影响。

不过我认为，这两种训练并没有矛盾，完全可以在 $\sigma_a = 0$ 的时候，用 slice score matching 来训练，看看效果如何。



## 实验

在这个部分，我们会详细说明这篇文章的实验。

一共有四个 benchmark 数据集用于测试，两个灰度，两个彩色。

BSD68：David Martin, Charless Fowlkes, Doron Tal, and Jitendra Malik. A database of human segmented natural images and its application to evaluating segmentation algorithms and measuring ecological statistics. In Proceedings Eighth IEEE International Conference on Computer Vision. ICCV 2001, volume 2, pages 416–423. IEEE, 2001.

Set12

CBSD68：David Martin, Charless Fowlkes, Doron Tal, and Jitendra Malik. A database of human segmented natural images and its application to evaluating segmentation algorithms and measuring ecological statistics. In Proceedings Eighth IEEE International Conference on Computer Vision. ICCV 2001, volume 2, pages 416–423. IEEE, 2001.

Kodak

训练的数据是 DIV2K：Radu Timofte, Shuhang Gu, Jiqing Wu, Luc Van Gool, Lei Zhang, Ming-Hsuan Yang, Muhammad Haris, et al. NTIRE 2018 challenge on Single Image Super-Resolution: Methods and Results. In The IEEE Conference on Computer Vision and Pattern Recognition (CVPR) Workshops, June 2018.

还有 (C)BSD400.

数据集的处理：裁剪成 128x128 patch 训练。 数据增广：random horizontal，vertical flip，flop method。对于灰度图，用的是 CBSD68 的灰度版本。我估计训练的时候，也是用的灰度版本。

DIV2K : https://data.vision.ee.ethz.ch/cvl/DIV2K/

CBSD400 : https://www2.eecs.berkeley.edu/Research/Projects/CS/vision/bsds/

Kodak : http://www.cs.albany.edu/xypan/research/snr/Kodak.html/

Set12 : https://www.researchgate.net/ﬁgure/12-images-from-Set12-dataset/ﬁg11/338424598



**所有的噪声都是合成的。**

为了公平，所有的比较方法都是用相同的模型 U-Net：Jaakko Lehtinen, Jacob Munkberg, Jon Hasselgren, Samuli Laine, Tero Karras, Miika Aittala, and Timo Aila. Noise2Noise: Learning image restoration without clean data. arXiv preprint arXiv:1803.04189, 2018. 来源是 Noise2Noise 的 U-Net。

> 这里很好奇，实际上 AR-DAE，应该用现在最先进的 score-based model 才是比较好的。

训练设置：100 epoch，Adam 优化器。前 50 epoch 是 2e-4，后 50 epoch 是 2e-5 的学习率。用 GTX 1080Ti 训练大概 10个小时。

对于 blind noise，as for the quality metric $Q(·)$  we use the TV norm for the Gaussian noises and its combination with data ﬁdelity term for the Poisson and Gamma noise.

实验包含三种噪声：高斯、泊松、gamma

高斯：比较对象有 BM3D，**Noise2Void，Noise2Self**，SURE，Noise2Noise，监督学习。已知参数时，噪声设为 25 和 50，未知时则是 [5, 55] 的随机采样的值（每个图的噪声水平可能都不同）。

Noise2Noise，监督学习肯定是最好的。

泊松：BM3D 和 SURE 换成了 Anscombe (BM3D+VST) 和 PURE，已知参数时，$\zeta=0.01$ 或 $\zeta=0.05$，未知参数时，[0.001, 0.1] 随机采样。

gamma 分布是类似的，但是 BM3D 和 SURE 没有对应的拓展（实际上 SURE 也有啊，用 Tweedie's formula 就可以啊）

在这个实验里，我们要关注的是 **Noise2Void，Noise2Self**，看起来实现会麻烦一点。

更多的**训练细节**：实际上，作者在训练的时候，网络用的是比较简单的，并没有包含 $\sigma_a$ 作为输入。作者训练的时候，先从较大的值开始训练，然后逐渐变成最小的。这一点从算法中可以看出来。

![屏幕快照 2021-12-13 下午7.54.59](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-12-13 下午7.54.59.png)

**对于已知噪声水平的时候，训练 score function 用的是同样的噪声水平的图片。如果是未知噪声水平，训练的时候，则是随机选择不同噪声的图片都来训练（那实际上，训练出来的 score function 是一个更复杂的 $y$ 的 score function，我认为这样就和原来的 Tweedie's formula 矛盾了。原来的 Tweedie's formula 的 $y$ 的 score function 可不是这样的。是固定一个噪声水平下的，否则推不出结论了。）**

关于 $\sigma_a^{\max}$ 和 $\sigma_a^{\min}$ 的选择，高斯和 gamma 是 [0.1, 0.001], 泊松是 [1, 0.05] 

图像质量评估函数：

高斯情形
$$
Q(\hat{x}(\sigma))=|\nabla \hat{x}(\sigma)|
$$
泊松情形
$$
Q(\hat{x}(\zeta))=\alpha|\nabla \hat{x}(\zeta)|+\hat{x}(\zeta)-y \log \hat{x}(\zeta)
$$
Gamma 情形
$$
Q(\hat{x}(k))=|\nabla \hat{x}(k)|+\alpha \frac{y}{\hat{x}(k)}+\frac{\beta}{2}\left(\frac{y}{\hat{x}(k)}\right)^{2}+\gamma \log \hat{x}(k)
$$
更多方法的比较：

作者还比较了 Noisier2Noise, Noise2Same 和 Samuli Laine, Tero Karras, Jaakko Lehtinen, and Timo Aila. High-quality self-supervised deep image denoising. arXiv preprint arXiv:1901.10277, 2019. 的两个版本（应该是这篇文章提出的两种方法）

还有一个实验是用于真实数据，FMD，这个数据的噪声使用 mixed Poisson-Gaussian. 作者的处理方法是先用高斯的 Tweedie's formula，然后用泊松的 Tweedie's formula。但是这也太不合理了吧。

难道不能推出新的 Tweedie's formula 吗？



## 参考文献

What Regularized Auto-Encoders Learn from the Data-Generating Distribution

AR-DAE-Towards Unbiased Neural Entropy Gradient Estimation

这两篇文献是关于估计 score function 的文献。

另外，很重要的是 tweedie's formula 的部分。

Aapo Hyvärinen and Peter Dayan. Estimation of non-normalized statistical models by score matching. Journal of Machine Learning Research, 6(4), 2005. 这篇文章提出了估计 score function 的方法。
