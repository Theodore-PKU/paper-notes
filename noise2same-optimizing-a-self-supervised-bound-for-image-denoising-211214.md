# Noise2Same: Optimizing A Self-Supervised Bound for Image Denoising

[TOC]

## 信息

作者：Yaochen Xie, Zhengyang Wang, Shuiwang Ji

年份：2020

期刊会议：NeurIPS 2020

Bibtex:

```latex
@article{xie2020noise2same,
  title={Noise2same: Optimizing a self-supervised bound for image denoising},
  author={Xie, Yaochen and Wang, Zhengyang and Ji, Shuiwang},
  journal={arXiv preprint arXiv:2010.11971},
  year={2020}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/noise2same-optimizing-a-self-supervised-bound-for-image-denoising.pdf)

code: https://github.com/divelab/Noise2Same



## 概括

通读。

这篇文章研究的是自监督去噪问题。首先，作者回顾了 Noise2Self 和 Noise2Void 等基于 J-invariant 的相关方法，指出他们实际上并没有严格满足 J-invariant 的性质。这是因为他们在inference 的时候不是严格按照 J-invariant 的形式进行 inference，仅仅是把 J-invariant 用于自监督训练。有的还增加了后处理的方法。作者分析了为什么 J-invariant 不好，但是我觉得这个部分基本都是废话。然后作者给出了一个没有 J-invariant 限制的 loss 上界（在 J-invariant 的条件下，两者是相等的），然后将这个上界作为 loss。这个上界我们发现和 SURE 有着密切的联系。



## 方法

这个部分，我们直接关注作者提出的 Noise2Same 是怎么做的。

基本的想法就是提出一个 upper bound。在 J-invariant 的框架中，这个 bound 是完全紧的。但是去掉这个条件之后，就不是了，所以作者增加了一项。这个在文章中的 Theorem 1 给出了详细的说明：
$$
\mathbb{E}_{x, y}\|f(\boldsymbol{x})-\boldsymbol{y}\|^{2}+\|\boldsymbol{x}-\boldsymbol{y}\|^{2} \leq \mathbb{E}_{x}\|f(\boldsymbol{x})-\boldsymbol{x}\|^{2}+2 m \mathbb{E}_{J}\left[\frac{\mathbb{E}_{x}\left\|f(\boldsymbol{x})_{J}-f\left(\boldsymbol{x}_{J c}\right)_{J}\right\|^{2}}{|J|}\right]^{1 / 2}
$$
条件是 noise 是 iid 0 均值的。这里的 $x$ 是把噪声标准化了。但是实验的时候怎么标准化？我觉得非常奇怪。这个定理实际上放缩地比较厉害。

基于这个定理，作者提出的损失函数就是：
$$
\mathcal{L}(f)=\mathbb{E}_{x}\|f(\boldsymbol{x})-\boldsymbol{x}\|^{2} / m+\lambda_{i n v} \mathbb{E}_{J}\left[\mathbb{E}_{x}\left\|f(\boldsymbol{x})_{J}-f\left(\boldsymbol{x}_{J^{c}}\right)_{J}\right\|^{2} /|J|\right]^{1 / 2}
$$
这里的 $\lambda_{inv}$ 实际上应该是 2，但是作者表示，这个值可以用来控制 J-invariant 的强弱，是一个可以调节的参数。不过如果没有对 $x$ 标准化，我感觉这个参数得调。

虽然作者表示，这个方法不基于 J-invariant，但实际上总是要给定一个 $\mathcal{J}$，因为不同的 $\mathcal{J}$ 损失函数就是不同的。

实际上，作者怎么处理呢，从作者给出的图可以看出，实际上还是每个像素看成一个集合。只不过训练的时候只是随机地从图像中选择一部分像素抹掉（这可以看成是简单化损失函数了）。

![屏幕快照 2021-12-14 下午4.45.23](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-12-14 下午4.45.23.png)

作者考虑了一个高斯噪声的情形来进一步分析（Theorem2）：
$$
\mathbb{E}_{x, y}\|f(\boldsymbol{x})-\boldsymbol{y}\|^{2}+\|\boldsymbol{x}-\boldsymbol{y}\|^{2} \leq \mathbb{E}_{x}\|f(\boldsymbol{x})-\boldsymbol{x}\|^{2}+2 m \sigma \mathbb{E}_{J}\left[\frac{\mathbb{E}\left\|f(\boldsymbol{x})_{J}-f\left(\boldsymbol{x}_{J^{c}}\right)_{J}\right\|^{2}}{|J|}\right]^{1 / 2}
$$
这里不要求 $x$ 是标准化的。

> 这下有意思了。这个式子的右边非常像 SURE 的损失函数。
>
> SURE 的 loss
> $$
> \ell_{S U R E}(\Theta)=\mathbb{E}_{y \sim P_{Y}}\left\{\left\|y-F_{\Theta}(y)\right\|^{2}+2 \sigma^{2} \operatorname{div}_{y} F_{\Theta}(y)\right\}
> $$
> 我们现在考虑 $\mathcal{J}$ 就是单个像素的集合。
>
> 那么Theorem2 的右边可以写成：
> $$
> \mathbb{E}_{x}\|f(\boldsymbol{x})-\boldsymbol{x}\|^{2}+2 \sigma^2 \sum_j \frac{1}{\sigma}\left[\mathbb{E}\left\|f(\boldsymbol{x})_{j}-f\left(\boldsymbol{x} + \Delta x_j\right)_{j}\right\|^{2}\right]^{1 / 2}
> $$
> 这里的 $\Delta x_j$ 表示经过作者的 Mask 策略之后 $j$ 位置的像素值和原来的像素值的差异。那么上述式子可以进一步改写成：
> $$
> \begin{align}
> &\mathbb{E}_{x}\|f(\boldsymbol{x})-\boldsymbol{x}\|^{2}+\mathbb{E}_x 2 \sigma^2  \sum_j \frac{\Delta x_j}{\sigma} \frac{| f(\boldsymbol{x})_{j}-f\left(\boldsymbol{x} + \Delta x_j\right)_{j}|}{\Delta x_j} \\
> \approx & \mathbb{E}_{x}\|f(\boldsymbol{x})-\boldsymbol{x}\|^{2}+\mathbb{E}_x 2 \sigma^2  \sum_j \frac{\Delta x_j}{\sigma} |\frac{\part f(\boldsymbol{x})_j}{\part x_j}| \\
> > & \mathbb{E}_{x}\|f(\boldsymbol{x})-\boldsymbol{x}\|^{2}+\mathbb{E}_x 2 \sigma^2  \sum_j \frac{\Delta x_j}{\sigma} \frac{\part f(\boldsymbol{x})_j}{\part x_j} \\
> \approx & \mathbb{E}_{x}\|f(\boldsymbol{x})-\boldsymbol{x}\|^{2} + \mathbb{E}_x 2 \sigma^2 \sum_j \frac{\part f(\boldsymbol{x})_j}{\part x_j} \\
> = & \mathbb{E}_{x}\|f(\boldsymbol{x})-\boldsymbol{x}\|^{2} + \mathbb{E}_x 2 \sigma^2 \textrm{div}_{\boldsymbol{x}} f(\boldsymbol{x})
> \end{align}
> $$
> 虽然这个推导非常不严谨，但是似乎作者提出的上界类似于 SURE 函数。

从本质上说，这个方法已经和 Noise2Self, Noise2Void 这一类方法完全不同了。因为它并没有改变输入的分布，而是增加了一个基于 J-invariant 的正则像。而且作者取的是一个上界。在 SURE 的理论中，我们是知道 SURE loss 等价于优化 Theorem1的左边的：
$$
\mathbb{E}_{x, y}\|f(\boldsymbol{x})-\boldsymbol{y}\|^{2}+\|\boldsymbol{x}-\boldsymbol{y}\|^{2} = \text{SURE loss}
$$
而从我们刚才的推导，可以隐约看出这里的差异，也就是把 div 的每一项，通过绝对值放大了。如果我们看Theorem1的证明关键，会发现有这么一步：
$$
\begin{aligned}
\frac{1}{|J|} \sum_{j \in J} \mathbb{E}_{y}\left[\operatorname{Cov}\left(f(\boldsymbol{x})_{j}, x_{j} \mid \boldsymbol{y}\right)\right] &=\frac{1}{|J|} \sum_{j \in J} \mathbb{E}_{y}\left[\operatorname{Cov}\left(f(\boldsymbol{x})_{j}-f\left(\boldsymbol{x}_{J^{c}}\right)_{j}, x_{j} \mid \boldsymbol{y}\right)\right] \\
& \leq \frac{1}{|J|} \sum_{j \in J} \mathbb{E}_{y}\left[\operatorname{Var}\left(f(\boldsymbol{x})_{j}-f\left(\boldsymbol{x}_{J^{c}}\right)_{j} \mid \boldsymbol{y}\right) \cdot \operatorname{Var}\left(x_{j} \mid \boldsymbol{y}\right)\right]^{1 / 2}
\end{aligned}
$$
这一步，首先是将 $\sum Cov$ 放缩成 $\sum |Cov|$，然后再利用 Cauchy-Schwarz inequality 得到下面的结果。柯西洗袜子不等式 $|\langle x, y\rangle| \leq\|x\| \cdot\|y\|$

Theorem 2 indicates that, when the noise is stronger with a larger σ, f should be more strictly J-invariant. Based on the deﬁnition of J-invariance, a more strictly J-invariant f will depend more on the context $x_{J^c}$ and less on the noisy input $x_J$ . 我认为作者的这个评价有点道理。

总结一下我的看法：从高斯噪声的角度，我认为这个方法非常接近于 SURE，可以说是 SURE 的一种特例。如果是其他噪声，我其实觉得效果不一定好。至于 Noise2Self，Noise2Void，肯定是已经知道比较差了。

**我们的分析是基于高斯情形的 SURE，但是作者显然是利用这个放缩，直接推广到了更复杂的噪声情形（任意的 blind noise），所以也和 SURE 有一些区别。即放缩了 SURE，然后直接应用其他噪声模型。**

作者的实验主要是和 BM3D, Noise2Self, Noise2Void,Noise2Noise, 监督学习比较。数据集是 BSD68 和 Imagenet， Hanzi，和其他的一个数据集。这里 Noise2Self, Noise2Void 仅仅是 mask replacement 的区别。其实我感觉是可以分析出这个策略的好坏的（正如我们之前用定理一分析的那样），作者说他们自己用的是高斯噪声替换，Noise2Self 用的是随机的像素值，Noise2Void 是随机的一个周围像素值。Noise2Self 还提出了一种是周围像素的均值。

RGB 自然图像（imagenet）的噪声是泊松+高斯+伯努利的混合噪声。

Hanzi 是高斯噪声

BSD68 也是高斯噪声

FMD 数据集的噪声模型不太清楚。

网络结构是 U-Net。

