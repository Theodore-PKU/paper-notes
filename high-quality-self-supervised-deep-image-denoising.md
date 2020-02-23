### Paper:

High-Quality Self-Supervised Deep Image Denoising

### Author:

Samuli Laine, Tero Karras, Jaakko Lehtinen, Timo Aila

### Year:

2019

### Notes:

这一篇和压缩感知相关度不大，因此没有细看，只注意了其中的思想。大致的想法是如果我们对噪声有足够清楚的认识，那么利用噪声均值为0的特性，我们可以利用周围像素来估计噪声的统计性质，换句话说，就是要计算 $p(x|y, \Omega_y)$， $x$ 是真实像素值，$y$ 是带噪声的像素值，$\Omega_y$ 是 $y$ 附近的像素值。如果用一般的标准监督回归的学习方式，可以用 corrupted-clean pairs 和 L2 损失函数来得到 $\mathbb{E}_{\boldsymbol{x}}\left[p\left(\boldsymbol{x} | \boldsymbol{y}, \Omega_{y}\right)\right]$. 在没有真实值的基础上，我们用 $y$ 作为 target 来估计 $\mathbb{E}_{\boldsymbol{x}}\left[p\left(\boldsymbol{x} |\Omega_{y}\right)\right]$. 在test的时候，我们再把 $y$ 的信息利用起来。

利用 $p\left(\boldsymbol{y} | \Omega_{y}\right)=\int p(\boldsymbol{y} | \boldsymbol{x}) p\left(\boldsymbol{x} | \Omega_{y}\right) \mathrm{d} \boldsymbol{x}$, 通过网络得到 $\Omega_y$ 到 $p\left(\boldsymbol{x} | \Omega_{y}\right) $ 的分布参数 $\mathcal{N}\left(\boldsymbol{\mu}_{x}, \boldsymbol{\Sigma}_{x}\right)$ 映射，这里用极大似然概率来训练网络 。测试的时候利用贝叶斯公式 $p\left(\boldsymbol{x} | \boldsymbol{y}, \Omega_{y}\right) \propto p(\boldsymbol{y} | \boldsymbol{x}), p\left(\boldsymbol{x} | \Omega_{y}\right)$，用极大后验概率的方式得到 $x$，即 $\mathbb{E}_{\boldsymbol{x}}\left[p\left(\boldsymbol{x} | \boldsymbol{y}, \Omega_{y}\right)\right]$。

### Bibtex:

```latex
@inproceedings{laine2019high,
  title={High-quality self-supervised deep image denoising},
  author={Laine, Samuli and Karras, Tero and Lehtinen, Jaakko and Aila, Timo},
  booktitle={Advances in Neural Information Processing Systems},
  pages={6968--6978},
  year={2019}
}
```