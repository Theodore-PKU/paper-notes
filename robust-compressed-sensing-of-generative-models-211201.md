# Robust Compressed Sensing of Generative Models

[TOC]

## 信息

作者：Ajil Jalal, Liu Liu, Alexandros G. Dimakis, Constantine Caramanis

年份：2020

期刊会议：NeurIPS

Bibtex:

```latex
@article{jalal2020robust,
  title={Robust compressed sensing of generative models},
  author={Jalal, Ajil and Liu, Liu and Dimakis, Alexandros G and Caramanis, Constantine},
  journal={arXiv preprint arXiv:2006.09461},
  year={2020}
}
```



## 概括

略读。

这篇文章是对 Bora 的压缩感知求解算法的进一步研究。Bora 的方法就是用一个训练好的生成器表示图像，直接优化压缩感知的 dc loss，属于 determined opitmization 的方法。作者认为这种方法对于特别的线性矩阵或特别的测量噪声是无效的，生成的结果会变差。这里的特别指的是 heavy tail，如果用统计的语言描述，就是高阶矩不是有限值。如果从一维的分布图来看，就是数轴两边衰减很慢。作者提出的方法不是直接对 dc loss 进行训练，而是采用 MOM loss 进行优化。MOM loss 根据我的理解，在没有噪声的情况下，或者正常情况下应该会等价于 dc loss，但是至于 heavy tail 的情况，为什么会有效，我没有搞懂的。MOM loss 可以简单表述为：将测量值分组，分别给出一个 dc loss，然后构造一个 min max 的优化问题。具体参考方法部分，如何优化这个问题，作者给出了一个很简单的算法。

这篇文章还类似于 Bora 他们关于一般压缩感知问题的求解保证提供了在 heavy tail 情况下的一些理论结果，但是我不太关心，故忽略。



## 方法

MOM loss。首先定义：
$$
\ell_{j}(z):=\frac{1}{b}\left\|A_{B_{j}} G(z)-y_{B_{j}}\right\|^{2}
$$
这里的 j 表示不同的测量值分组。作者的分组是尽量平均的。G 是生成器，y 是测量值。b 是每一组测量值的数量。

定义的优化问题是：
$$
\widehat{z}=\arg \min _{z \in \mathbb{R}^{k}} \max _{z^{\prime} \in \mathbb{R}^{k}} \operatorname{median}_{1 \leq j \leq M}\left(\ell_{j}(z)-\ell_{j}\left(z^{\prime}\right)\right) .
$$

> max median 说明 z’ 更满足 dc，这样才能让 max median 比较大，同时 min 表示 z 也会向 z’ 靠近（或者说也接近 dc）。
> 倘若 z 满足 dc，那么 z’ 要满足就得是 z’ 也满足 dc，那么这个 min max 就是 0，如果 z 不满足 dc，z’ 满足 dc 是 max median 的解，因此这不是最小的，因此理论上这个东西的最小值就是 z 满足 dc。但是因为测量值有噪声，这会导致满足 dc 的解离 prior 的 range 比较远。但问题是总是可以训练 z 使得 loss 为 0 啊。除非 prior 的 range 真的非常好，以至于 z 很难达到 dc 的情况，但是如果是这样，使用 min max 有什么特别的地方呢？

![屏幕快照 2021-12-01 下午10.03.50](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-12-01 下午10.03.50.png)