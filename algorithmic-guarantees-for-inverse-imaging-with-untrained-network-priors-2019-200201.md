### Paper:

Algorithmic Guarantees for Inverse Imaging with Untrained Network Priors

### Author:

Gauri Jagatap and Chinmay Hegde

### Year:

2019

### Notes:

阅读时间：2020.02.01

泛读。这篇文章研究的是 DIP 的理论性质。这篇文章似乎发到 nips 上了，但是我仍然认为这篇文章缺乏价值。这篇文章试图证明DIP的计算框架可以用来实现压缩感知重建，并且能够给出一个看起来很不错的理论。假设 S 表示网络 G 的值域，作者用的引理是，若 G 是一个 decoder，并且 rank(A) 满足一定的条件（这个条件随着网络参数增加而增加，随着 d 增加而增加），那么 A 在 S 上有 RIP 性质。只要真值 x 属于 S，那么很容易证明 DIP 框架可以收敛到比较好的结果。但是实际上，为了让 x 属于 S，网络就得很大，相应的 rank(A) 很大才能满足 RIP 条件，这和实际的要求不符。因此当实际要求的 rank(A) 较小时，即使 x 属于 S，此时A在 S 上并不满足 RIP，条件，因此收敛就没有保证了。这就和我的实验结果，G 仍然可以生成非常好的 zero-fill 的结果相吻合。

下面对文章的内容进行逻辑梳理。只考虑压缩感知的部分（作者实际上还讨论了 compressive phase retrieval）

首先作者给出在 S 上的 RIP 定义。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-01%E4%B8%8B%E5%8D%883.14.1.png"/>

然后给出 decoder 和 A 在什么条件下，A 满足 S 上的 RIP，注意，这里的 $k_l$ 和网络大小有关，因此网络越大，n 也就越大。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-01%E4%B8%8B%E5%8D%883.14.8.png"/>

只要 A 满足 S 上的 RIP 条件，那么用算法1得到的下式的解就是我们想要的重建信号。

<img src="http://latex.codecogs.com/svg.latex? \min _{x}\|y-A x\|^{2} \quad \text { s.t. } \quad x=G(\mathbf{w} ; z)" border="0"/>

算法1:

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-01%E4%B8%8B%E5%8D%883.13.5.png"/>

### Bibtex:

```latex
@article{jagatap2019algorithmic,
  title={Algorithmic guarantees for inverse imaging with untrained network priors},
  author={Jagatap, Gauri and Hegde, Chinmay},
  journal={arXiv preprint arXiv:1906.08763},
  year={2019}
}
```

