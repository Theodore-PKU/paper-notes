### On the number of linear regions of deep neural networks

Author: Razvan Pascanu, Guido Montúfar, Kyunghyun Cho, Yoshua Bengio

Year: 2014

Published in: NIPS 2014

```latex
@inproceedings{montufar2014number,
  title={On the number of linear regions of deep neural networks},
  author={Montufar, Guido F and Pascanu, Razvan and Cho, Kyunghyun and Bengio, Yoshua},
  booktitle={Advances in neural information processing systems},
  pages={2924--2932},
  year={2014}
}
```

Link: https://github.com/Theodore-PKU/paper-notes/blob/master/on-the-number-of-linear-regions-of-deep-neural-networks.md

Note: /Users/xieyutong/Documents/Research/PaperReading/Notes/on-the-number-of-linear-regions-of-deep-neural-networks.md



这篇文章的主要内容有两个，一个是 ReLU 网络的最大划分数量的上界和下界，另一个是 maxout 网络的划分数量的讨论。后者不是我们研究的重点所以不考虑。不过关于 maxout 网络，提一句：表示的是凸函数。

ReLU 网络的部分出了上界和下界，最开始还有一个直观上的讨论。这部分的讨论是以折叠的角度进行的。大意是说输入空间中的不同区域，因为某一层映射到相同的区域，这就相当于折叠，而之后的划分就会同时作用在原始空间的不同区域，产生一个复合的效果，使得划分的效率变高。类似的，下一层可以继续将不同的子区域映射到输出的相同区域，以形成对子区域的进一步划分。比如下图：

![image-20200421220353446](/Users/xieyutong/Pictures/screenshot/image-20200421220353446.png)

不过这个分析，并没有出现在后面的证明中。

**上界**: 作者的结论很简单 $2^N$，N 是节点的数量，也就是超平面的数量。

**下界**: 结论是
$$
\left(\prod_{i=1}^{L-1}\left\lfloor\frac{n_{i}}{n_{0}}\right\rfloor^{n_{0}}\right) \sum_{j=0}^{n_{0}}\left(\begin{array}{c}
n_{L} \\
j
\end{array}\right)
$$

-----

> 证明用到了 Bounding and Counting Linear Regions of Deep Neural Networks 这篇文章中提到的 z-pattern 的构造。这里简单描述一下。首先，网络的描述是 $n_0$ 维输入，每一层 $n_i > n_0$. 通过构造网络来分析。构造方法是先将 $n_i$ 个节点分组，分为 $n_0$  组，每组 $ p = \left\lfloor\frac{n_{i}}{n_{0}}\right\rfloor$ 个节点，每组的划分效果是对某一维度（暂时假定输入的维度是 $n_0$）进行划分，划分数量为 $p$（在 Bounding and Counting Linear Regions of Deep Neural Networks 这篇文章中变成了 $p+1$）。这样以来，就有 $\left\lfloor\frac{n_{i}}{n_{0}}\right\rfloor^{n_0}$ 个划分的区域。每组的划分效果是：
> $$
> \begin{aligned}
> h_{1}(\mathbf{x}) &=\max \{0, \mathbf{w} \mathbf{x}\} \\
> h_{2}(\mathbf{x}) &=\max \{0,2 \mathbf{w} \mathbf{x}-1\} \\
> h_{3}(\mathbf{x}) &=\max \{0,2 \mathbf{w} \mathbf{x}-2\} \\
> & \vdots \\
> h_{p}(\mathbf{x}) &=\max \{0,2 \mathbf{w} \mathbf{x}-(p-1)\}
> \end{aligned}
> $$
> $w$ 只有一个分量为 1，其余为 0。下面通过一个线性函数得到 z-曲线。
> $$
> \tilde{h}_{j}(\mathbf{x})=\left[1,-1,1, \ldots,(-1)^{p-1}\right]\left[h_{1}(\mathbf{x}), h_{2}(\mathbf{x}), h_{3}(\mathbf{x}), \ldots, h_{p}(\mathbf{x})\right]^{\top}
> $$
> <img src="/Users/xieyutong/Pictures/screenshot/image-20200421222014522.png" alt="image-20200421222014522" style="zoom:50%;" />
>
> 这样即使是 $n_i$ 输出，又重新变成了 $n_0$ 维的输出，就可以在此基础上重新进行空间划分，这一步的线性变换和 $\tilde{h}_j$ 合并构成一个 $l+1$ 层的线性变换。以此类推得到最后的划分数量。
>
> 证毕。



