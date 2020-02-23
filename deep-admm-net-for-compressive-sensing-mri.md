### Paper:

Deep ADMM-Net for Compressive Sensing MRI

### Author:

Yan Yang, Jian Sun, Huibin Li, Zongben Xu

### Year:

2016

### Notes:

这篇文章的思路仍然是 unfold 一个传统算法来实现重建MRI。之前有人只训练非线性shrinkage函数，这篇文章是对所有的参数都进行训练。unfold 的算法是ADMM算法。

原始的目标函数：
$$
\hat{x}=\underset{x}{\arg \min }\left\{\frac{1}{2}\|A x-y\|_{2}^{2}+\sum_{l=1}^{L} \lambda_{l} g\left(D_{l} x\right)\right\}
$$
这里考虑了多个变换。ADMM的解为：
$$
\left\{\begin{array}{l}
{\mathbf{X}^{(\mathbf{n})}: x^{(n)}=F^{T}\left[P^{T} P+\sum_{l=1}^{L} \rho_{l} F D_{l}^{T} D_{l} F^{T}\right]^{-1}\left[P^{T} y+\sum_{l=1}^{L} \rho_{l} F D_{l}^{T}\left(z_{l}^{(n-1)}-\beta_{l}^{(n-1)}\right)\right]} \\
{\mathbf{Z}^{(\mathbf{n})}: z_{l}^{(n)}=S\left(D_{l} x^{(n)}+\beta_{l}^{(n-1)} ; \lambda_{l} / \rho_{l}\right)} \\
{\mathbf{M}^{(\mathbf{n})}: \beta_{l}^{(n)}=\beta_{l}^{(n-1)}+\eta_{l}\left(D_{l} x^{(n)}-z_{l}^{(n)}\right)}
\end{array}\right.
$$

> 这里需要说明的是shrinkage函数和正则项的关系。
>
> 在数学上，shrinkage函数和正则函数是对应的，所以学到一个shrinkage 函数也对应学到一个正则函数，这样就不用去训练正则函数了。不过有一个问题是shrinkage函数的类型和正则函数之间如何确定？如果在一个函数子空间下学到的shrinkage函数，对应的正则函数是不是也是限制在某个子空间下？换句话说，如果只考虑如何学习正则函数如何？
>
> 生成模型是否可以作为正则项的一种载体？

作者将 $D_lx^n$ 的运算用模块 C 表示（也就是说作者考虑的变换是局部的卷积变换）。因此有四个操作。重建 $\mathbf{X}^{(n)}$，卷积运算 $\mathbf{C}^{(n)}$，非线性变换（shrinkage函数）$\mathbf{Z}^{(n)}$，乘子更新 $\mathbf{M}^{(n)}$。需要说明的是，重建步中的变换算子从原来的 $D_l$ 变成了 $H_l$，和卷积运算的是不一样，分开训练。非线性变换用的是分片线性函数，因此参数是结点。乘子更新的步长是参数。
$$
x^{(n)}=F^{T}\left(P^{T} P+\sum_{l=1}^{L} \rho_{l}^{(n)} F H_{l}^{(n) T} H_{l}^{(n)} F^{T}\right)^{-1}\left[P^{T} y+\sum_{l=1}^{L} \rho_{l}^{(n)} F H_{l}^{(n) T}\left(z_{l}^{(n-1)}-\beta_{l}^{(n-1)}\right)\right]
$$

$$
c_{l}^{(n)}=D_{l}^{(n)} x^{(n)}
$$

$$
z_{l}^{(n)}=S_{P L F}\left(c_{l}^{(n)}+\beta_{l}^{(n-1)} ;\left\{p_{i}, q_{l, i}^{(n)}\right\}_{i=1}^{N_{c}}\right)
$$

$$
\beta_{l}^{(n)}=\beta_{l}^{(n-1)}+\eta_{l}^{(n)}\left(c_{l}^{(n)}-z_{l}^{(n)}\right)
$$

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-23%E4%B8%8B%E5%8D%883.11.43.png)

学习到的shrinkage函数和卷积变换：

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-23%E4%B8%8B%E5%8D%883.20.55.png)

网络初始化：用一个传统的ADMM算法的参数设定来初始化所有的运算模块。

训练方法：基于BP的求导，使用 L-BFGS 算法。作者花了比较多的篇幅讲求导的问题。

损失函数：NMSE

关于实验，作者实验了不同的欠采样率，和不同的方法进行了比较。另外探究了网络深度、卷积大小的影响。特别的是，作者用脑部数据训练，在胸部数据上检验泛化性，结果还很不错，说明学习到的shrinkage函数、卷积变换、步长等都很有效。不过值得指出的，BM3D-MRI 的方法表现在指标上仍然是最好的，但是时间消耗太大了。这是唯一的缺点。

### Bibtex:

```latex
@inproceedings{sun2016deep,
  title={Deep ADMM-Net for compressive sensing MRI},
  author={Sun, Jian and Li, Huibin and Xu, Zongben and others},
  booktitle={Advances in neural information processing systems},
  pages={10--18},
  year={2016}
}
```

