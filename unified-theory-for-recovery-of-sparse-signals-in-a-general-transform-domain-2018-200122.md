### Paper:

Uniﬁed Theory for Recovery of Sparse Signals in a General Transform Domain

### Author:

Kiryung Lee, Yanjun Li, Kyong Hwan Jin, and Jong Chul Ye

### Year:

2018

### Notes:

阅读时间：2020.01.22

泛读。这篇文章作者研究的是稀疏信号重建的理论。作者首先考虑的是一个比CS理论更一般的情形，也就是先不假设 RIP 等条件，对测量矩阵没有什么特别的要求，给出了一个 uniform random sampling 的稀疏信号恢复的测量数量的下限（以及相关的一些结论）。在这个结论中，incoherence 仍然起到一个非常重要的作用，对于 incoherence 很大的测量矩阵，测量数量的下限就很大。作者给出的第二个主要结论中，作者使用了 variable 的 sampling 方式，概率和 incoherence 相关，此时，稀疏信号恢复时的测量数量的下限由 incoherence 的均值决定。根据这种采样方式，作者给出了相应的一些结论。最后作者还拓展到 group sparsity（也就是由多个变换矩阵的情形）。所有的实验都是基于传统的优化问题的概念，使用 ADMM 算法求解。

需要学习一些关于 incoherence 的概念。因此需要看传统压缩感知理论。

### Bibtex:

```latex
@article{lee2018unified,
  title={Unified Theory for Recovery of Sparse Signals in a General Transform Domain},
  author={Lee, Kiryung and Li, Yanjun and Jin, Kyong Hwan and Ye, Jong Chul},
  journal={IEEE Transactions on Information Theory},
  volume={64},
  number={8},
  pages={5457--5477},
  year={2018},
  publisher={IEEE}
}
```

