### Paper:

One-dimensional Deep Image Prior for Time Series Inverse Problems

### Author:

Sriram Ravula, Alexandros G. Dimakis

### Year:

2019

### Notes:

阅读时间：2020.02.02

泛读。这篇文章研究的是 1D 的信号逆问题，使用的方法是 DIP 的框架，只不过是把 2D 的卷积网络换成了 1D 的卷积网络，另外加上了 TV 正则项。作者考虑了高斯采样的压缩感知、去噪、inputation、undersampled DCT 测量等实验，结果表明效果都很不错。

> 这篇文章让人惊讶的不是方法，而是 1D 信号同样适用 DIP 的框架。这不禁让人思考 DIP 有效的原因。我认为 DIP 的有效要从函数表示的角度入手，猜测 DIP 的参数就是在求解一个类似线性方程的系统，只不过是局部、非线性的。在每个局部都是一个超定方程（而且超定的程度很高），多个超定方程构成一个超定程度较低的方程），这样以来，对于随机的输入，总是可以找到一组解。为什么 DIP 一般比较快的生成低频部分或简单部分，应该就是因为 SGD 算法在求解超定方程时的特性吧，并不是网络有什么正则性，而是重建信号本身的性质和超定方程求解过程中达成了某种一致性。如果按照这样的解释，也可以用一些较为简单的模型来证明一些结论，也可以设计各种实验来验证。

### Bibtex:

```latex
@article{ravula2019one,
  title={One-dimensional deep image prior for time series inverse problems},
  author={Ravula, Sriram and Dimakis, Alexandros G},
  journal={arXiv preprint arXiv:1904.08594},
  year={2019}
}
```

