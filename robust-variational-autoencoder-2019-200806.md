# Robust Variational Autoencoder

Author: Haleh Akrami, Anand A. Joshi, Jian Li, Sergul Aydore, Richard M. Leahy

Year: 2019

Published in: NeurIPS 2019

```latex
@article{akrami2019robust,
  title={Robust variational autoencoder},
  author={Akrami, Haleh and Joshi, Anand A and Li, Jian and Aydore, Sergul and Leahy, Richard M},
  journal={arXiv preprint arXiv:1905.09961},
  year={2019}
}	
```

Link: https://github.com/Theodore-PKU/paper-notes/blob/master/robust-variational-autoencoder-2019-200806.md

Note: /Users/xieyutong/Documents/Research/PaperReading/Notes/robust-variational-autoencoder-2019-200806.md



这篇文章表述起来并不难，但是数学推导的细节很多。笔记里只说明思想。

首先是文章的动机。对于 VAE 模型，如果数据集中存在 outlier，会影响模型的表现。事实上，我们想要的效果应该是对于 outlier，其重建结果应该很差，因为它们不属于我们想要重建出来的分布。但是一般的 VAE 使用了 KL 散度作为 cost function，导致这些 outlier 会被当作原始分布的一部分，所以也会生成 outlier。因此作者考虑借助鲁棒统计的概念改进 VAE 的训练。做法非常直接，用 $\beta$ 散度替换 KL 散度。其目的是对 outlier 鲁棒。而一个鲁棒的 VAE 也可以用于 outlier 的检测，只要它的重建结果和原始信号相差很大，那么这就是一个 outlier。

作者做了一个 toy 实验，训练网络表示高斯分布。使用另一个 mode 的高斯分布作为 outlier 放入训练集中。结果表明传统 VAE 会学到 outlier 的分布，导致学到的分布有两个 mode，而作者提出的 RVAE 则会学到单峰的分布。

对于 $\beta$ 散度，作者说当这个参数 $\beta$ 趋近于 0 时会退化成 KL 散度。其形式如下：
$$
D_{\beta}\left(\hat{p}(\mathbf{X}) \| p_{\theta}(\mathbf{X} \mid \mathbf{Z})\right)=\frac{1}{\beta} \int_{X} \hat{p}(\mathbf{X})^{\beta+1} d \mathbf{X} \\-\frac{\beta+1}{\beta} \int_{\mathbf{X}} \hat{p}(\mathbf{X}) p_{\theta}(\mathbf{X} \mid \mathbf{Z})^{\beta} d \mathbf{X}+\int_{\mathbf{X}} p_{\theta}(\mathbf{X} \mid \mathbf{Z})^{\beta+1} d \mathbf{X}
$$
使用 $\beta$ 散度的结果和原来 VAE 的差别在于输出部分。

这是原来的 cost function：
$$
\begin{aligned} L\left(\theta, \phi ; \mathbf{x}^{(i)}\right)=& \mathbb{E}_{q_{\phi}\left(\mathbf{Z} \mid \mathbf{x}^{(i)}\right)}\left[\log \left(p_{\theta}\left(\mathbf{x}^{(i)} \mid \mathbf{Z}\right)\right)\right] \\ &-D_{K L}\left(q_{\phi}\left(\mathbf{Z} \mid \mathbf{x}^{(i)}\right) \| p_{\theta}(\mathbf{Z})\right) \end{aligned}
$$
这是改进后的 cost function：
$$
\begin{aligned} L_{\beta}(q, \theta)=&-N \mathbb{E}_{q}\left[\left(H_{\beta}\left(\hat{p}(\mathbf{X}) \| p_{\theta}(\mathbf{X} \mid \mathbf{Z})\right)\right)\right] \\ &-D_{K L}\left(q(\mathbf{Z}) \| p_{\theta}(\mathbf{Z})\right) \end{aligned}
$$
文章剩下的内容分为两个部分，一个是高斯分布和伯努利分布情形下 cost function 的推导，一个是实验。