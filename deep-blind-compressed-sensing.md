### Paper:

Deep Blind Compressed Sensing

### Author:

Shikha Singh, Vanika Singhal and Angshul Majumdar

### Year:

2016

### Notes:

这篇文章的主要内容是要在压缩感知的测量值域做信息提取。方法是Deep matrix factorization。
$$
\min _{D_{1}, D_{2}, \ldots, D_{M}, Z}\left\|Y-A D_{1} D_{2} \ldots D_{M} Z\right\|_{F}^{2}+\lambda\|Z\|_{1}
$$
要优化的就是上式。使用了交替迭代的方法。为了保证D在合理的范围内，还有范数的约束。Z是latent factor，也就是系数，是信号X的表示。

作者做了一些实验来验证这种表示的能力，比较对象是自编码等网络。其他的方法都是先重建，然后在进行表示学习。

### Bibtex:

```latex
@article{singh2016deep,
  title={Deep blind compressed sensing},
  author={Singh, Shikha and Singhal, Vanika and Majumdar, Angshul},
  journal={arXiv preprint arXiv:1612.07453},
  year={2016}
}
```