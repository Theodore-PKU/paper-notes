### Paper:

DNNs for Sparse Coding and Dictionary Learning

### Author:

Subhadip Mukherjee, Debabrata Mahapatra, and Chandra Sekhar Seelamantula

### Year:

2017

### Notes:

这篇文章的作者和[Deep Sparse Coding Using Optimized Linear Expansion of Thresholds](deep-sparse-coding-using-optimized-linear-expansion-of-thresholds) 的作者是一样的，并且这两篇文章讨论的内容几乎一致。

总的来说就是讲proximal gradient 算法的迭代式子展开为网络结构，固定矩阵运算的部分，只训练激活函数（shrinkage）。学习一个激活函数或者shrinkage函数就是学习一个编码的prior。作者使用的函数拟合方法为：
$$
\psi^{t}(u)=\sum_{k=1}^{K} c_{k}^{t} \phi_{k}(u), \text { where } \phi_{k}(u)=u e^{-\frac{(k-1) u^{2}}{2 \tau^{2}}}
$$
如果每一层的激活函数都一样，就是 Fixed 版本
如果每一层都不一样，就是 Var 版本。

作者还考虑了如果测量矩阵 A 未知的情况。这种情况下就不是训练激活函数。激活函数是固定的hard-threholding，训练的内容是 A。

太没意思了。

### Bibtex:

```latex
@inproceedings{mukherjee2017dnns,
  title={DNNs for sparse coding and dictionary learning},
  author={Mukherjee, Subhadip and Mahapatra, Debabrata and Seelamantula, Chandra Sekhar},
  booktitle={NIPS Bayesian Deep Learning Workshop},
  year={2017}
}
```