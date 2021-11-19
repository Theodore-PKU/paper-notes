# On Fast Sampling of Diffusion Probabilistic Models

[TOC]

## 信息

作者：Zhifeng Kong, Wei Ping

期刊会议: Under review

年份: 2021

Bibtex:

```latex
@article{kong2021fast,
  title={On Fast Sampling of Diffusion Probabilistic Models},
  author={Kong, Zhifeng and Ping, Wei},
  journal={arXiv preprint arXiv:2106.00132},
  year={2021}
}
```

[Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/on-fast-sampling-of-diffusion-probabilistic-models.pdf)



## 概括

详读。

这篇文章提出了一种 ddpm 的快速采样的方法 FastDPM。这种方法和此前的一些方法有一些联系。这个方法考虑的主要是 ddpm 的方法，和 score-based 方法略有区别。作者的想法：首先是将原本离散的时间和扩散过程的系数连续化，这样时刻不一定是整数，也可以是实数。给定了时间和扩散过程的连续化表示后，作者提出了两种采样方法，称为 VAR 和 STEP，详细内容参考后文。总的来说，还是可以理解成是从 ddpm 的扩散过程中如何确定更少采样的点（时刻）的方法。

在实验的部分，作者测试了这种方法在不同数据集、不同生成领域的效果。

总的来说，这种快速采样的方法是一种经验性的方法，并没有理论上的分析来证明哪一种快速采样的方法是有效的。



## 方法

首先对目前主流的两种快速采样方法进行说明。



作者提出的 FastDPM 方法，需要定义扩散步和噪声水平之间的映射。在通常的 ddpm 模型的设定中，采用如下的扩散过程：
$$
x_{t} = \sqrt{\bar{\alpha}_{t}} x_{0} + \sqrt{1 - \bar{\alpha}_{t}}\epsilon, \epsilon \sim \mathcal{N} (0, 1)
$$
因此，可以用 $\sqrt{\bar{\alpha}_{t}}$ 来表示当前的噪声水平。换言之，当原本的一个 ddpm 扩散过程序列（从 $t = 0$ 到 $t = T$）确定后，给定 $\sqrt{\bar{\alpha}_{t}}$，就可以确定对应的时间 $t$. 因此定义函数 $\mathcal{R}(t)$。不过现在这个函数仅仅定义在整数上。

假设原来扩散过程的 variance 序列是 $\left\{\beta_{t}\right\}_{t=1}^{T}$，**并且是线性的**，那么可以表示成 $\beta_{i} = \beta_{1} + (i - 1) \Delta \beta$。注意，通常情况下，$\beta_{T}$ 也非常小。于是我们可以计算出 $\Delta \beta = \frac{\beta_{T} - \beta_{1}}{T-1}$。进一步，我们可以定义一个非常大的常数 $\hat{\beta} = \frac{1 - \beta_{1}}{\Delta \beta}$，这个数可以理解成需要多少步，$\beta$ 会变成 1. 在这些记号下，就可以重新推到出下面的表达式：
$$
\begin{aligned}
\bar{\alpha}_{t} &=\prod_{i=1}^{t}\left(1-\beta_{i}\right)=\prod_{i=1}^{t}\left(1-\beta_{1}-(i-1) \Delta \beta\right)=(\Delta \beta)^{t} \prod_{i=0}^{t-1}(\hat{\beta}-i) \\
&=(\Delta \beta)^{t} \Gamma(\hat{\beta}+1) \Gamma(\hat{\beta}-t+1)^{-1} .
\end{aligned}
$$
$\Gamma$ 函数定义在实数域上，于是，当 $t$ 不是整数的时候，仍然可以计算。因此就可以推广这个定义到实数域上，得到 $\mathcal{R}$ 的新的定义：
$$
\mathcal{R}(t)=(\Delta \beta)^{\frac{t}{2}} \Gamma(\hat{\beta}+1)^{\frac{1}{2}} \Gamma(\hat{\beta}-t+1)^{-\frac{1}{2}}
$$
对于原来的整数 $t$ 上述式子的结果和原本 ddpm 的扩散过程是一致的。但这个式子对于任意实数 $t$ 都可以给出一个和扩散过程的噪声水平有关的值。**从本质上来说，这是在 variance 序列是线性的条件下，对 $\mathcal{R}(t)$ 刚好可以用 $\Gamma$ 函数拟合。换句话说，我们甚至可以先定义一个 $\mathcal{R}(t)$，然后直接用 $\mathcal{R}(t)$ 定义扩散过程的系数。**这样一来，ddpm 的训练就和时刻的关系分离开来，我认为这在实践中可能可以大大提高训练的效率。不需要每次都是用给定的 1000 个时刻。而且实际上，这篇文章提出的方法，在实践的时候，也还是用了连续情况下的分布来计算。完全可以将训练连续化，采样考虑是某一种独立的离散化。

回到这篇论文，当 $\mathcal{R}(t)$ 给定以后，就可以推出它的反函数。当然反函数 $\mathcal{T}(r)$ 没有解析的表达形式，需要用数值的方法进行计算。作者说，可以用二分法来计算，很快就可以达到较高精度。

当我们有了整个连续时间上的扩散过程的参数，就可以推出任意两个时刻之间的分布的关系。在论文中，作者还是用离散的形式来表示（下标是离散的），但实际上，可以直接看成是一个连续的时间 $t$ 的下标。假设有一个序列，长度为 $S$，值满足 $1 > r_1 > r_2 > \cdots > r_S > 0$. 这里可以看成新的时刻，但更准确地说，应该看成 $r_{i}$ 对应的时间 $t$ 时刻。但用整数的下标表示的好处是，和 ddpm 的原始形式更接近。
$$
\hat{x}_{s} \sim \mathcal{N}\left(\hat{x}_{s} ; r_{s} \hat{x}_{0},\left(1-r_{s}^{2}\right) I\right) .
$$
这里加了一个 $\hat{}$ 的原因是为了和原始 ddpm 的表示区别开来。当然 $\hat{x}_{0} \sim p_{data}$ 和 $x_{0}$ 是一样的。我们也可以推出：
$$
q\left(\hat{x}_{s} \mid \hat{x}_{s-1}\right)=\mathcal{N}\left(\hat{x}_{s} ; \sqrt{1-\eta_{s}} \hat{x}_{s-1}, \eta_{s} I\right)=\mathcal{N}\left(\hat{x}_{s} ; \frac{r_{s}}{r_{s-1}} \hat{x}_{s-1},\left(1-\frac{r_{s}^{2}}{r_{s-1}^{2}}\right) I\right)
$$
实际上，这个式子表示的是两个相邻的离散时刻之间的条件分布。这个条件分布和 $\{r_i\}_{i=1}^{S}$ 的序列是互推的。$\gamma_{s}$ 可以表示为 $r^2_s/r^2_{s-1}$, $\eta_{s} = 1 - \gamma_{s}$. 显然，这两个新的参数对应的就是 $\alpha_{t}, \beta_{t}$。

当然我们还可以推出 $q ( \hat{x}_{s-1} \mid \hat{x}_{s}, \hat{x}_{0})$，不过作者没有给出来。而是直接给出了 ddpm 模型的后验条件分布
$$
p_{\theta}\left(\hat{x}_{s-1} \mid \hat{x}_{s}\right)=\mathcal{N}\left(\hat{x}_{s-1} ; \frac{1}{\sqrt{\gamma_{s}}}\left(\hat{x}_{s}-\frac{\eta_{s}}{\sqrt{1-\bar{\gamma}_{s}}} \epsilon_{\theta}\left(\hat{x}_{s}, \mathcal{T}\left(r_{s}\right)\right)\right), \tilde{\eta}_{s} I\right)
$$
这个式子实际上表明，对于一个新的时刻（可能是连续的某个实数），我们也可以估计噪声，只不过时间的输入必须改成 $\mathcal{T}(r_s)$。而且这个式子的形式和原始 ddpm 的形式没有任何区别。

不管怎么说，有了上述的分析，实际上我们可以从这个新的序列 $\{r_i\}_{i=1}^{S}$ 出发，进行一次采样，只要 $S$ 较小，采样的速度就快了。不过怎么定义新的序列呢？作者实际上给出了两种具体的方案，无法从理论上判断这两种方法哪一种是最合适的，因此作者用实验来判断。

这两种序列分别是：

1. Noise levels from variances (VAR)，也就是先定义 $\eta_s$，然后推导其他参数。$\gamma_{s}=1-\eta_{s}$ and $\bar{\gamma}_{s}=\prod_{i=1}^{s} \gamma_{i}$, $r_{s}=\sqrt{\bar{\gamma}_{s}}$
2. Noise levels from steps (STEP)，先定义原来的时刻序列 1, ..., T 的一个子序列 $\{\tau_{s}\}$（也就是说，采用了连续整数），则有 $r_{s}=\mathcal{R}\left(\tau_{s}\right)=\sqrt{\bar{\alpha}_{\tau_{s}}}$

STEP 是 VAR 的一个特例。实际上，这两种方法还是没有给出具体的形式，因为怎么定义 $\eta_{s}$, 怎么定义子序列都没有说。

在实验部分，作者对 VAR，STEP 都给出了两种形式：

![屏幕快照 2021-11-19 下午10.07.34](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-11-19 下午10.07.34.png)

总之，还是一种经验性的想法。

关于采样的实际算法，如果用 ddpm 的原始形式，每次迭代都需要增加一个噪声，如果是 ddim 的采样形式，增加的噪声乘上了一个系数。ddpm 是 ddim 的一个特例。

ddpm 的形式：
$$
\hat{x}_{s-1}=\frac{1}{\sqrt{\gamma_{s}}}\left(\hat{x}_{s}-\frac{\eta_{s}}{\sqrt{1-\bar{\gamma}_{s}}} \epsilon_{\theta}\left(\hat{x}_{s}, \mathcal{T}\left(r_{s}\right)\right)\right)+\sqrt{\tilde{\eta}_{s}} \hat{\epsilon}_{s}
$$
ddim 的形式：
$$
\hat{x}_{s-1}=\sqrt{\bar{\gamma}_{s-1}}\left(\frac{\hat{x}_{s}-\sqrt{1-\bar{\gamma}_{s}} \epsilon_{\theta}\left(\hat{x}_{s}, \mathcal{T}\left(r_{s}\right)\right)}{\sqrt{\bar{\gamma}}}\right)+\sqrt{1-\bar{\gamma}_{s-1}-\kappa^{2} \tilde{\eta}_{s}} \epsilon_{\theta}\left(\hat{x}_{s}, \mathcal{T}\left(r_{s}\right)\right)+\kappa \sqrt{\tilde{\eta}_{s}} \hat{\epsilon}_{s}
$$
作者的实验就是在不同的领域，对不同的数据集，使用不同的快速采样的策略以及不同的采样方法，进行比较。最后得出了一些结论：

VAR marginally outperforms STEP for small S.

ddim 在图像域更好，ddpm 在音频域更好。

不过这些结论都是实验性的。

