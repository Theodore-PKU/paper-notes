# Generative Flows with Matrix Exponential

[TOC]

## 信息

Authors: Changyi Xiao and Ligang Liu

Year: ICML 2020

Bibtex:

```
@inproceedings{xiao2020generative,
  title={Generative Flows with Matrix Exponential},
  author={Xiao, Changyi and Liu, Ligang},
  booktitle={International Conference on Machine Learning},
  pages={10452--10461},
  year={2020},
  organization={PMLR}
}
```

cite: 0

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/generative-flows-with-matrix-exponential.pdf)



## 概括

这篇文章的方法其实和另外一篇 convolution exponential 的想法基本是一样的，但是使用方便又一些区别。matrix exponential 的方法就是利用矩阵的指数是可逆矩阵并且 Jacobian 矩阵的行列式、逆矩阵都很好计算。由于指数展开有无穷项，这篇文章的算法里仍然是取前 k 项。另外，作者表示，由于这些矩阵实际计算出来的时候值都比较小，所以取前 k 项的误差也会比较小。在 convolution exponential 那篇文章中，仅仅将矩阵的指数化用在替换 glow 模型的 1x1 卷积。这篇文章不仅仅替换了 1x1 卷积，还替换了 coupling layer 的系数，此时 coupling layer 不再是 affine 的形式，而是矩阵乘法的形式（因为参数网络的输出是一个矩阵 W，实际运算使用 exp(W)）。另外，为了让矩阵 W 的参数较少（也就是参数网络的输出维数），作者对 W 有一些限制。具体来说就是 exp(W) 仅仅作用在 size 为 1x1 的位置，也就是类似于 1x1 卷积，并且令 W = A1A2 两个矩阵的乘积，这两个矩阵都是低秩矩阵。



## 方法

矩阵的指数化：
$$
e^{W}=\sum_{i=0}^{\infty} \frac{W^{i}}{i !}
$$
主要的性质是：$(e^{W})^{-1} = e^{-W}, \log \det (e^W) = Tr(W)$

![屏幕快照 2021-04-09 下午9.41.35](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-09 下午9.41.35.png)

这个表格很好地说明了 matrix exp 应用的地方。对于 1x1 卷积的部分，没有什么特别的地方。

为了减少参数，作者令 $W = A_1 A_2$，而且 $A_1 \in \mathbb{R}^{n\times t}, A_2 \in \mathbb{R}^{t \times n}$，只要 t 很小，W 的参数量就会比 $n^2$ 少很多。

传统的 affine coupling layer 的形式是一个线性的形式，这篇文章则变成：
$$
\left(\begin{array}{c}
\boldsymbol{y}_{d+1} \\
\vdots \\
\boldsymbol{y}_{n}
\end{array}\right)=\boldsymbol{e}^{\boldsymbol{S}\left(\boldsymbol{x}_{1: d}\right)}\left(\begin{array}{c}
\boldsymbol{x}_{d+1} \\
\vdots \\
\boldsymbol{x}_{n}
\end{array}\right)+\boldsymbol{b}\left(\boldsymbol{x}_{1: d}\right)
$$
 

显然，由于 $\boldsymbol{S}_{(x_{1:d})}$ 矩阵是从 $x_{1:d}$ 获得的，所以 Jacobian 矩阵是一个三角矩阵。

如果 x 的维数是 h x w x 2c，那么矩阵 S 就会很大。因此作者限制其仅仅作用在局部点上，因此有：
$$
\begin{aligned}
\boldsymbol{y}^{1} &=\boldsymbol{x}^{1} \\
\forall i, j, \boldsymbol{y}_{i, j,:}^{2} &=\boldsymbol{e}^{\boldsymbol{S}\left(\boldsymbol{x}^{1}\right)_{i, j, :,:}} \boldsymbol{x}_{i, j,:}^{2}+\boldsymbol{b}\left(\boldsymbol{x}^{1}\right)_{i, j,}
\end{aligned}
$$
这样一来，矩阵 S 就属于 $\mathbb{R}^{h\times w\times c \times c}$. 更进一步，如果使用 $W = A_1 A_2$ 的形式，那么 S 的参数就会更少。换言之用 $x_{1:d}$ 生成的参数就更少了。

关于 1x1 卷积，就没有什么很特别的地方。作者说，对于 W 的初始化是 skew-symmetric matrix，这个矩阵的特点不太清楚，就不管了。