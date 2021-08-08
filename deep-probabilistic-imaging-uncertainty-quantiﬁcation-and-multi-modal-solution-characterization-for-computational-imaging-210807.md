# Deep Probabilistic Imaging: Uncertainty Quantiﬁcation and Multi-modal Solution Characterization for Computational Imaging

[TOC]

## 信息

Authors: He Sun, Katherine L. Bouman

Year: AAAI 2020

Bibtex:

```latex
@article{sun2020deep,
  title={Deep probabilistic imaging: Uncertainty quantification and multi-modal solution characterization for computational imaging},
  author={Sun, He and Bouman, Katherine L},
  journal={arXiv preprint arXiv:2010.14462},
  volume={9},
  year={2020}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/deep-probabilistic-imaging-uncertainty-quantiﬁcation-and-multi-modal-solution-characterization-for-computational-imaging.pdf)

code：http:// imaging.cms.caltech.edu/dpi/



## 概括

这篇文章的基本想法是借助 flow-based 模型来表示后验分布，用变分模型的正则项来表示先验分布。如果仅仅知道先验分布和 data consistency，并不能直接对后验分布进行 sample。作者的做法则是将 flow-based 模型表示的后验分布和通过贝叶斯公式计算的后验分布计算 KL 散度，使其极小化。以这种方式实现后验分布的采样。也就是说，先验分布和后验分布是两个独立的模型。作者的实验主要是黑洞成像的实验，MRI 的实验讲的比较少。



## 方法

作者提出的方法属于 variational Bayesian 方法。

首先作者用 flow-based 生成模型来表示图像分布。
$$
x \sim q_{\theta}(x) \quad \Leftrightarrow \quad x=G_{\theta}(z), z \sim \mathcal{N}(0,1)
$$
然后令 $q_{\theta}(x)$ 和后验分布 $P(x|y)$ 的 KL 散度最小化（这似乎是把 $q_{\theta}$ 当成是一个条件分布了）

这个目标函数可以化简为：
$$
\begin{aligned}
&\theta^{\star}=\arg \min _{\theta} D_{\mathrm{KL}}\left(q_{\theta}(x) \| p(x \mid y)\right)\\
&=\arg \min _{\theta} \int q_{\theta}(x)\left[\log q_{\theta}(x)-\log p(x \mid y)\right] d x\\
&=\arg \min _{\theta} \int q_{\theta}(x)\left[\log q_{\theta}(x)-\log p(y \mid x)-\log p(x)\right] d x\\
&=\arg \min _{\theta} \mathbb{E}_{x \sim q_{\theta}(x)}\left[-\log p(y \mid x)-\log p(x)+\log q_{\theta}(x)\right]
\end{aligned}
$$
这个损失函数可以看成是一个 MAP loss 的期望加上一个 entropy encouragement 项（$H_{\theta}= E_{x\sim q_{\theta}(x)} [\log q_{\theta}(x)]$）。

但是这个损失函数中，$p(x), q_{\theta}(x)$ 都是未知的。恰好，由于生成模型是 flow 模型，所以 $q_{\theta}$ 可以计算概率。$p(x)$ 本质上和变分模型的正则项是对应的（作者在论文中也分析了变分模型的贝叶斯视角，也就是说正则项其实就是控制先验概率）。由此，上面的损失函数就可以写为：
$$
\begin{aligned}
\theta^{\star}=\arg &\min _{\theta} \mathbb{E}_{z \sim \mathcal{N}(0,1)}\left\{-\log p\left(y \mid G_{\theta}(z)\right)-\log p\left(G_{\theta}(z)\right)\right.\\
&\left.+\log \pi(z)-\log \left|\operatorname{det} \frac{d G_{\theta}(z)}{d z}\right|\right\}
\end{aligned}
$$

$$
\begin{gathered}
\theta^{\star}=\arg \min _{\theta} \sum_{k=1}^{N}\left\{\mathcal{L}\left(y, f\left(G_{\theta}\left(z_{k}\right)\right)\right)+\lambda \mathcal{R}\left(G_{\theta}\left(z_{k}\right)\right)\right. \\
\left.\qquad-\log \left|\operatorname{det} \frac{d G_{\theta}\left(z_{k}\right)}{d z_{k}}\right|\right\}
\end{gathered}
$$

从这个写法来看，作者的想法可以理解为用正则项表示先验分布（正则项本身就可以看成是概率相关了，而且这里只要计算 log 就行，不需要考虑归一化常数），另一方面，后验分布（条件分布）则是直接用一个 flow 模型来表示。因此这个模型表示的是给定 y 的情况下的后验分布的估计。一旦 theta 训练好了，就可以直接通过 flow 模型 $G_{\theta}$ 来 sample，还可以计算概率了。另外，第二个式子里忽略了 log \pi(z)，这是因为它的期望是常数。

作者表示，如果 data fitting loss (L) 和正则项并不是和真实情况完全匹配，那么我们计算出来的模型仅仅是一个估计。另一方面，作者说损失函数中的第三项，和 beta-VAE 非常相似，所以引入了一个 beta 参数来控制生成的 diversity。
$$
\begin{gathered}
\theta^{\star}=\arg \min _{\theta} \sum_{k=1}^{N}\left\{\mathcal{L}\left(y, f\left(G_{\theta}\left(z_{k}\right)\right)\right)+\lambda \mathcal{R}\left(G_{\theta}\left(z_{k}\right)\right)\right. \\
\left.\qquad-\beta\log \left|\operatorname{det} \frac{d G_{\theta}\left(z_{k}\right)}{d z_{k}}\right|\right\}
\end{gathered}
$$
beta 越大，diversity 越大。更大的 beta 有利用训练的时候快速收敛。



## 实验

作者的实验包含toy实验、黑洞和MRI，我们这里近几年考虑 MRI。

MRI 的数据似乎就是 fastMRI，resize 到 64x64 了。

使用的结构和正则项是：

A Real-NVP network architecture similar to Fig. 3 with 32 afﬁne coupling layers is used to approximate the image posterior, and a total variation (TV) regularizer is applied as the image prior.

不过 beta 具体怎么调好像没有说。



## 参考文献

Repetti, A.; Pereyra, M.; and Wiaux, Y. 2019. Scalable bayesian uncertainty quantiﬁcation in imaging inverse problems via convex optimization. SIAM Journal on Imaging Sciences 12(1):87–118. 这篇是所谓的 Bayesian Hypothesis Testing 方法。

Gershman, S.; Hoffman, M.; and Blei, D. 2012. Nonparametric variational inference. arXiv preprint arXiv:1206.4665. 这篇是 Variational Bayesian Methods 方法。

Xue, Y.; Cheng, S.; Li, Y.; and Tian, L. 2019. Reliable deep-learning-based phase imaging with uncertainty quantiﬁcation. Optica 6(5):618–629.  是贝叶斯网络方法。但是这个不确定性和我们讨论的不一样。