### Paper:

Compressed Sensing with Deep Image Prior and Learned Regularization

### Author:

Dave Van Veen, Ajil Jalal, Mahdi Soltanolkotabi, Eric Price, Sriram Vishwanath, Alexandros G. Dimakis

### Year:

2018

### Notes:

阅读日期：2020.01.16

泛读。这篇文章之前读过了。研究的是无监督的CS重建，使用的方法是 DIP，也就是在已知测量值的情况下，对网络参数进行优化，使得固定随机输入 z 可以生成信号，并且信号的测量值和已知的测量值接近。和 Bora 的 ambient GAN 非常类似，只不过 ambient GAN 是训练好网络然后搜索 z，而无监督的 DIP 方法则不需要训练好网络，也不需要对 z 进行搜索。只需要对给定的 z 找到最合适的参数 w。在训练时作者还加上了正则项。其中一个正则项是通过少量样本学到的（估计网络参数 w 的均值和方差）。

目标函数：

<img src="http://latex.codecogs.com/svg.latex? w^{*}=\underset{w}{\arg \min }\|y-A G(z ; w)\|^{2}+R\left(w ; \lambda_{T}, \lambda_{L}\right)" border="0"/>

其中，

<img src="http://latex.codecogs.com/svg.latex? R\left(w ; \lambda_{T}, \lambda_{L}\right)=\lambda_{T} T V(G(z ; w))+\lambda_{L} L R(w)" border="0"/>

作者用 MAP 推导出 LR(w) 的形式为：

<img src="http://latex.codecogs.com/svg.latex? L R(w)=(w-\mu)^{T} \Sigma^{-1}(w-\mu)" border="0"/>

为了估计 $\mu$ 和 $\Sigma$，作者用少量数据先训练网络，得到多个网络参数。对这些参数，逐层计算均值和方差（这样可以避免需要计算的协方差矩阵太大，实际上就可以看成协方差矩阵是分块的，或者说每一层的网络参数是独立的）。

> DIP 的方法应该是利用了卷积神经网络的一些特性。如果信号不满足某些条件，或者和神经网络的结构没有某种内在联系，这个方法应当会失效。可以设计网络来验证一些结论。

### Bibtex:

```latex
@article{van2018compressed,
  title={Compressed sensing with deep image prior and learned regularization},
  author={Van Veen, Dave and Jalal, Ajil and Soltanolkotabi, Mahdi and Price, Eric and Vishwanath, Sriram and Dimakis, Alexandros G},
  journal={arXiv preprint arXiv:1806.06438},
  year={2018}
}
```

