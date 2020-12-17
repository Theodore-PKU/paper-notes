# Made: Masked autoencoder for distribution estimation

[TOC]

### 信息

Authors: Mathieu Germain, Karol Gregor, Iain Murray and Hugo Larochelle

Université de Sherbrooke, DeepMind and University of Edinburgh

ICML 2015

```latex
@inproceedings{germain2015made,
  title={Made: Masked autoencoder for distribution estimation},
  author={Germain, Mathieu and Gregor, Karol and Murray, Iain and Larochelle, Hugo},
  booktitle={International Conference on Machine Learning},
  pages={881--889},
  year={2015}
}
```

引用 300+

[Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/made-masked-autoencoder-for-distribution-estimation.pdf) 



### 概括

这篇文章整体模型是一个 Auto-encdoer 的结构。输入是 x，输出也是 x，但是输出的 x 表示的是一个分布（或者和分布有关的参数）。AE 的结构采用了自回归的限制。因此也可以看成是将一个自回归模型改成了 AE 的结构。实际上，自回归模型的输入就是 x，输出是 x 各个分量的条件概率。在一般的自回归模型中，实际的网络结构更类似于一个序列模型，但是在这篇文章中，作者利用参数 mask 矩阵的技巧实现用一个 AE 的结构表示自回归模型。

自回归模型的核心有两点：1. 信号的分量有一定的顺序；2. 当前分量的概率条件于此前的所有分量。这两个问题在这篇文章的 AE 模型中通过两个途径实现。1. 对信号的分量进行随机重排，不同的重排对应不同的顺序。2. 网络是全连接网络，但是每个节点会随机分配一个随机整数，表示这个节点在计算路径上只能由更小的 index 的分量计算得到，通过这些分配的随机整数（在 IAF 的文章中，称为 degree），就可以构造出层与层之间连接的权重 mask 矩阵，也就是限制哪些节点可以连接。这是很容易理解的，连接的节点必须满足输入节点的 degree 小于输出节点的 degree。很显然最终输入层和最终输出层的 degree 是固定的。由于采用了随机 degree 的技巧，实际在训练的时候，每次迭代都会使用不同的 degree，而且分量的顺序也是随机的，所以这种做法更像是一个多个自回归模型的集成模型。

作者在论文中也考虑了全连接网络的一些变体，但是这不是文章的主要内容。

评述：因为使用了全连接网络，信号的维度是有限的，而且网络的表达能力也不够。这篇文章虽然把自回归模型改写成一个 AE 模型，目的是加快概率计算。一般的自回归模型计算是很慢的，而且不能实现分布式训练。不过我个人对于自回归模型的理解是这种建模方式本身不够合理，从直觉上我不看好这种模型，因为图像不是序列数据。



### 方法

由于这篇文章考虑的是 MNIST 数据集，所以是二值图像，因此条件分布的建模在计算损失函数的时候，采用的是 cross-entropy 函数。

在考虑了自回归模型后，损失函数可以写成：
$$
\begin{equation}
\begin{aligned}
-\log p(\mathbf{x})=& \sum_{d=1}^{D}-\log p\left(x_{d} \mid \mathbf{x}_{<d}\right) \\
=& \sum_{d=1}^{D}-x_{d} \log p\left(x_{d}=1 \mid \mathbf{x}_{<d}\right) -\left(1-x_{d}\right) \log p\left(x_{d}=0 \mid \mathbf{x}_{<d}\right) \\
=& \ell(\mathbf{x})
\end{aligned}
\end{equation}
$$
对于只有一个隐层的全连接网络，只要对权重加上 mask 矩阵，很容易实现自回归的性质。即输出的第 i 个分量只和前 i - 1 个输入分量有关。不过这里因为涉及到输出的第 0 个分量和最后一个分量，对于隐层的 degree 分配，只能是 1 ～ D-1，D 是输入的维度。假设 m(k) 表示的是隐层的第 k 个节点的分配 degree

输入层的 mask 矩阵
$$
\begin{equation}
M_{k, d}^{\mathbf{W}}=1_{m(k) \geq d}=\left\{\begin{array}{ll}
1 & \text { if } m(k) \geq d \\
0 & \text { otherwise }
\end{array}\right.
\end{equation}
$$
输出层的 mask 矩阵
$$
\begin{equation}
M_{d, k}^{\mathrm{V}}=1_{d>m(k)}=\left\{\begin{array}{ll}
1 & \text { if } d>m(k) \\
0 & \text { otherwise }
\end{array}\right.
\end{equation}
$$
m(k) 的分配是每个节点随机从 1 到 D-1 选择一个。

对于多层的全连接网络，只要对每一个隐层的 degree 分配完成之后，根据输入层和输出层的 degree 的分配构造 mask 矩阵即可。这宗构造就是输入层的 degree 小于输出层的 degree
$$
\begin{equation}
M_{k^{\prime}, k}^{\mathbf{W}^{l}}=1_{m^{l}\left(k^{\prime}\right) \geq m^{l-1}(k)}=\left\{\begin{array}{ll}
1 & \text { if } m^{l}\left(k^{\prime}\right) \geq m^{l-1}(k) \\
0 & \text { otherwise }
\end{array}\right.
\end{equation}
$$
正如概括中所说，因为 degree 是随机的，所以可以构造一个集成模型。最后 test 的时候，可以对不同的分量顺序、不同的 degree 分配计算不同的结果，最后取平均。（但是这种做法，反而完全抛弃了自回归模型本身的性质，更像是利用自回归的结构设计了一种全连接网络的边的连接方式）。

另外，作者表示对于 mask 矩阵，如果随机的范围太大，会过拟合。作者的解决方案是限制 mask 可能出现的具体值的个数，每次随机只在限定数量的 mask 中随机。

最后给一个网络的示意图：

![屏幕快照 2020-12-17 上午10.22.46](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-17 上午10.22.46.png)