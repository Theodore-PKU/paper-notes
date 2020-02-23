### Paper:

Maximal Sparsity with Deep Networks?

### Author:

Bo Xin, Yizhou Wang, Wen Gao, Baoyuan Wang, David Wipf

### Year:

2016

### Notes:

这篇文章的主要内容是如何用深度网络来求解0范数问题。作者首先讨论了压缩感知理论中测量矩阵的相关条件不满足的情形，作者认为可以用深度学习来解决。对此，作者分析了一些理论性质，但是没有觉得有什么启发性或有用。

作者基于 $\boldsymbol{x}^{(t+1)}=f\left[\boldsymbol{\Psi} \boldsymbol{x}^{(t)}+\boldsymbol{\Gamma y}\right]$ 这个式子来展开。作者使用的是LSTM (也比较了ResNet结构)，把0范数问题当作一个预测问题，确定哪些idx非零。

### Bibtex:

```latex
@inproceedings{xin2016maximal,
  title={Maximal sparsity with deep networks?},
  author={Xin, Bo and Wang, Yizhou and Gao, Wen and Wipf, David and Wang, Baoyuan},
  booktitle={Advances in Neural Information Processing Systems},
  pages={4340--4348},
  year={2016}
}
```