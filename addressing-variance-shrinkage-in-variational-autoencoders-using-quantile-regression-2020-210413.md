# Addressing Variance shrinkage in Variational Autoencoders using Quantile Regression

[TOC]

## 信息

Authors: Haleh Akrami, Anand A. Joshi, Sergul Aydore

Year: 2020

Bibtex:

```
@article{akrami2020addressing,
  title={Addressing Variance Shrinkage in Variational Autoencoders using Quantile Regression},
  author={Akrami, Haleh and Joshi, Anand A and Aydore, Sergul and Leahy, Richard M},
  journal={arXiv preprint arXiv:2010.09042},
  year={2020}
}
```

cite: 0

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/addressing-variance-shrinkage-in-variational-autoencoders-using-quantile-regression.pdf)



## 概括

这篇文章的目的是利用 VAE 实现异常检测。但是这篇文章指出传统的 VAE 训练中存在 variance shrinkage 的问题。这会导致 variance 的估计是不准确的，也很难确定什么样的重建 loss 是异常的。为了解决这个问题，作者提出了使用 quantile regression 的损失函数替代原来的重建 loss。作者考虑了三个实验来验证 quantile regression 的有效性，其中最后一个实验是用来做异常检测。这篇文章比较好懂，但是我觉得存在一些问题。首先为什么 VAE 给定一个输入 x，最终的输出的分布可以用来衡量 variance 呢？这个 variance 又是什么呢？我认为异常检测的本质应该是判断 x 的概率是不是足够大，和训练的真实数据相同或者判断差异有多大。那么应该直接计算 x 的概率。但 VAE 不能实现这一点，因为真实概率中有一项是无法计算的（估计的后验分布和真实的后验分布的 KL 散度），要不然就是要对所有的 z 做一个积分才可以。所以我认为这种做法是不可行的。关于 variance shrinkage 的问题，为什么不能很小？对它的分析我觉得没有任何道理，本身就是重建，如果给定了隐变量，x 关于 z 的条件分布当然可以 variance 很小，凭什么要求它变大呢？这些都是非常奇怪的。另外关于 quantile regression 的损失函数，没有看出来这个损失函数的实际意义是什么，为什么极小化它可以实现 reconstruction loss。也许这就是李老师说的犯了和我们同样的错误。用 VAE 这样的生成模型来做异常检测，我认为很好的一种思路，但是应该不是这篇文章的做法。



## 方法

这里仅仅是对 quantile regression 进行说明。关于 variance shrinkage 的问题，直观理解是很容易的。因为要极小化 reconstruction loss，很自然就会尽量让最终的 x|z 条件分布的 variance 很小。

quantile 就是分位值。简单来说，这个方法就是估计高斯分布的两个分位值。对于高斯分布而言，只需要两个分位值就可以确定高斯分布了。作者假设 x|z 是高斯分布，所以只估计两个分位值。
$$
L_{R E C}=\sum_{i} \rho_{L}\left(x_{i}-f_{\theta_{L}}\left(x_{i}\right)\right)+\sum \rho_{H}\left(x_{i}-f_{\theta_{H}}\left(x_{i}\right)\right)
$$
其中 rho 的形式是：
$$
\rho_{\alpha}(y, \hat{y}):=\left\{\begin{array}{ll}
\alpha(y-\hat{y}) & \text { if }(y-\hat{y})>0 \\
(1-\alpha)(y-\hat{y}) & \text { otherwise }
\end{array}\right.
$$
这里其实就怪怪的，本来有一个逗号，但是在 (1) 中却不存在。很奇怪。L 和 H 对应两个不同的分位值。但是仔细分析 (2) 式，不知道为什么 minimize 这个函数会实现某种 reconstruct loss。

代码就不附上了，但是这里提到了一些 MRI 的数据集，可能有用。

For the training, we use 20 central axial slices of brain MRI datasets from a combination of 119 subjects from the Maryland MagNeTS study (Gullapalli, 2011) of neurotrauma and 112 subjects from the TrackTBI-Pilot (Yue et al., 2013) dataset, both available for download from https://fitbir.nih.gov.