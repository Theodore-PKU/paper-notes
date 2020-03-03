### Paper:

Compressive Sensing via Convolutional Factor Analysis

### Author:

Xin Yuan, Yunchen Pu, and Lawrence Carin

### Year:

2017

### Notes:

这篇文章是通过卷积因子分析(convolutional factor analysis) 的角度来恢复信号。卷积因子分析本身是一个字典学习或稀疏表示的形式。即字典是已卷积的形式出现。作者先说明了如何用ADMM算法求解信号表示和压缩感知（使用卷积字典来表示）。之后引入深度结构。引入深度结构的含义是多次进行卷积因子分析的表示（也就是多层），但是其中的一些内容没有解释清楚，因此不知道具体如何训练如何运作。比如 unpool 运算，作者在第7页中的解释没有看懂。另外作者认为可以用多层的卷积因子分析中的系数作为特征，来解决分类问题等。

作者的多层结构：
$$
\mathbf{X}_{n}^{\ell}=\sum_{k_{\ell}=1}^{K_{\ell}} \mathbf{D}_{k_{\ell}}^{\ell} * \mathbf{S}_{k_{\ell}, n}^{\ell}+\mathbf{E}_{n}^{\ell}, \quad \mathbf{S}_{n}^{\ell}=\text { unpool }\left(\mathbf{X}_{n}^{\ell+1}\right)
$$
总之，这篇文章启发性不强，关键的多层结构的关联和训练方式也没有说清楚。似乎是每一层可以单独训练 greedy 或者一起训练。unpool 大概是指如何将上一层的稀疏表示的系数传递到下一层中。

### Bibtex:

```latex
@article{yuan2017compressive,
  title={Compressive Sensing via Convolutional Factor Analysis},
  author={Yuan, Xin and Pu, Yunchen and Carin, Lawrence},
  journal={arXiv preprint arXiv:1701.03006},
  year={2017}
}
```