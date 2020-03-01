### Paper:

Global Guarantees for Enforcing Deep Generative Priors by Empirical Risk

### Author:

Paul Hand and Vladislav Voroninski

### Year:

2019

### Notes:

阅读日期：2020.03.01

泛读。这篇文章研究的是 bora 方法的理论性质。假设有一个生成网络 G（由卷积和激活函数 relu 构成，没有 bias），输入是 latent space 的 x，输出是信号 y，假设真实信号属于 G 的值域，作者证明，如果 G 的参数是高斯随机的，测量矩阵 A 也是高斯随机矩阵，那么测量值数量 m 和 G 都满足一定的条件时，对于测量值 Ay，Bora 方法的目标函数总是存在梯度，也就是不存在局部极小或鞍点。但是，仔细看这个定理的条件，核心是：m > C(kd log(n1*n2\*..\*nd))，d 是网络的层数，k 是 x 的维数，ni 是每一层的输出的维数。可以发现仍然陷入一个网络表达能力和测量值的矛盾。另外作者没有考虑卷积，因此作者的网络结构的假设太过简单了。当然，这样的证明，从另一个角度说明了 Bora 方法的局限性。证明就不看了。

### Bibtex:

```latex
@article{hand2019global,
  title={Global guarantees for enforcing deep generative priors by empirical risk},
  author={Hand, Paul and Voroninski, Vladislav},
  journal={IEEE Transactions on Information Theory},
  volume={66},
  number={1},
  pages={401--418},
  year={2019},
  publisher={IEEE}
}
```

### Related work

Alice Lucas, Michael Iliadis, Rafael Molina, and Aggelos K Katsaggelos. Using deep neural networks for inverse problems in imaging: Beyond analytical methods. IEEE Signal Processing Magazine, 35(1):20–36, 2018. 这篇文章是关于深度学习在逆问题中应用的 review，有空可以考虑看。

### Code or link

none