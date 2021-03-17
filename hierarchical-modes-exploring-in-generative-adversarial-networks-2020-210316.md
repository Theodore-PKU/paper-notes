## Hierarchical Modes Exploring in Generative Adversarial Networks

[TOC]

## 信息

Authors: Mengxiao Hu, Jinlong Li, Maolin Hu and Tao Hu

Year: AAAI 2020

Bibtex:

```
@inproceedings{hu2020hierarchical,
  title={Hierarchical Modes Exploring in Generative Adversarial Networks},
  author={Hu, Mengxiao and Li, Jinlong and Hu, Maolin and Hu, Tao},
  booktitle={Proceedings of the AAAI Conference on Artificial Intelligence},
  volume={34},
  number={07},
  pages={10981--10988},
  year={2020}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/hierarchical-modes-exploring-in-generative-adversarial-networks.pdf)



## 概括

这篇文章题目虽然叫做 Hierarchical，但是实际上和 Hierarchical 的关系不是很大。这篇文章目的是减少 conditional GAN 中的 mode collapse 的现象出现，采用的方法是增加一个正则项。这个正则项的目的是增加生成结果的多样性。mode collapse 的意思是说在生成的时候，可能会出现仅仅生成图像分布中的一部分，训练就收敛了。正则项简单地概括起来就是计算不同的隐变量在网络不同层的输出的距离和隐变量的距离的比值，这个比值控制了多样性。更具体一点，作者计算了相邻层的输出的距离的比值，然后通过设定比值的一个合理的值，在训练的时候让实际的距离比值和设定的比值接近（通过 1 范数）。

不参考这篇文章。



## 方法

以往通过正则项解决 mode collapse 的方法采用如下的做法：
$$
\begin{equation}
\mathcal{L}_{d}=\left\|\frac{d^{(1)}\left(\mathbf{z}_{1}, \mathbf{z}_{2}\right)}{d^{(n)}\left(G^{(n)}\left(\mathbf{z}_{1}\right), G^{(n)}\left(\mathbf{z}_{2}\right)\right)}\right\|_{1}
\end{equation}
$$
这里的 $d$ 表示距离度量，$n$ 表示的是生成器的最后一层。

作者提出来的这个距离比值，则不是一个，是多个，通过相邻层的输出来计算。对于 $i$ 为 0 的情况就是初始的隐变量。 
$$
\begin{equation}
r a t i o^{(i)}=\frac{d^{(i-1)}\left(G^{(i-1)}\left(\mathbf{z}_{1}\right), G^{(i-1)}\left(\mathbf{z}_{2}\right)\right)}{d^{(i)}\left(G^{(i)}\left(\mathbf{z}_{1}\right), G^{(i)}\left(\mathbf{z}_{2}\right)\right)}
\end{equation}
$$

$$
\begin{equation}
\mathcal{L}_{h}=\sum_{i=2}^{n} \| \text { ratio }^{(i)}-\lambda^{(i)} \|_{1}
\end{equation}
$$

这篇文章还比较了这两个正则项，以及怎么设定 $\lambda$ 的问题。但这篇文章提出的方法并不是我所喜欢的方法，而且这个方法也对自己的工作几乎没有作用。不过 mode collapse 的定义了解了。