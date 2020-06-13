### On the number of response regions of deep feedforward networks with piecewise linear activations

Author: Razvan Pascanu, Guido Montúfar, Yoshua Bengio

Year: 2014

Published in: ICLR 2014

```latex
@article{pascanu2013number,
  title={On the number of response regions of deep feed forward networks with piece-wise linear activations},
  author={Pascanu, Razvan and Montufar, Guido and Bengio, Yoshua},
  journal={arXiv preprint arXiv:1312.6098},
  year={2013}
}
```

Link: https://github.com/Theodore-PKU/paper-notes/blob/master/on-the-number-of-response-regions-of-deep-feedforward-networks-with-piecewise-linear-activations.md

Note: /Users/xieyutong/Documents/Research/PaperReading/Notes/on-the-number-of-response-regions-of-deep-feedforward-networks-with-piecewise-linear-activations.md



这篇文章讨论的是 ReLU MLP 表示的分片线性函数的分片数量，比较 shallow 网络（输入 $n_0$ 维，隐层 $kn$）和深层网络（输入 $n_0$ 维，隐层 $n$ 个节点，$k$ 层）的差别。

**结论**：前者是 $O(k^{n_0}n^{n_0})$，后者是 $\Omega(\left\lfloor n / n_{0}\right\rfloor^{k-1} n^{n_{0}})$。比较可以发现，当 $n$ 或 $k$ 变大时，后者比前者增长得更快。

**记号**：$\boldsymbol{n} =(n_0,n_1,\ldots, n_L)$ 表示一个 ReLU MLP 的各层维度，该网络表示的函数集合定义为 $\mathcal{F}_{\boldsymbol{n}}$。

region 的定义：定义一个开集 $R$，$f$ 限制在 $R$ 上是线性的，但是包含开集 $R$ 的其他开集 $\tilde{R}$，$f$ 的限制不是线性的。则这篇文章讨论的分片数量就是 region 的数量。这和激活 pattern 的定义还是不一样的。

#### 前期准备

三个 引理

引理 1: 设 $f = (f_i)_{i\in[k]}$ 是一个输出为 $k$ 元的函数，每个 $f_i$ 对应的 region 划分是 $R^i = {R^i_1, ...,R^i_{N_i}}$，则对于 $f$ 而言，region 的划分应当是 $\left\{R_{j_{1}, \ldots, j_{k}}=R_{j_{1}}^{1} \cap \cdots \cap R_{j_{k}}^{k}\right\}_{\left(j_{1}, \ldots, j_{k}\right) \in\left[N_{1}\right] \times \cdots \times\left[N_{k}\right]}$ 

> 这个引理是非常显然的。

引理 2: $f$ 的 region 数量和它能实现的最大数量之间是无关的

> 这个 引理的证明没看懂，但是这不是显然的吗？

引理 3: affine 函数可以由两个 affine 函数复合而成 。

> 线性代数的显然结论。

#### 只有一层隐层

这个结果已经很清楚了，就是 $r(\mathcal{A})=\sum_{s=0}^{n_{0}}\left(\begin{array}{l}
m \\
s
\end{array}\right)$

#### MLP

这部分有两个核心命题。

命题 4: 设输出神经元个数是 $n$，那么存在一个空间划分，使得任意 $1\leq a < b \leq n$，在这个空间划分下，有一个区域满足其激活 pattern 是 $R^+ = \{a, a+1, ..., b\}$

命题 5: 对于 $\mathbb{R}^{n_0}$ 的任意 $p$ 个区域 $R_i$（不是划分之后的区域），存在 $p$ 个映射 $g_i: \boldsymbol{y} \rightarrow \boldsymbol{U_i} \boldsymbol{y} + \boldsymbol{c}$，满足 $g_i(R_i)$ 包含单位球。

用这两个命题可以证明定理 1 和下一节的一个特殊结论。

定理 1: 划分的数量至少是 $\left(\prod_{i=1}^{k-1}\left\lfloor\frac{n_{i}}{n_{0}}\right\rfloor\right) \sum_{i=0}^{n_{0}}\left(\begin{array}{c}
n_{k} \\
i
\end{array}\right)$

------

> 证明思路：整体概括是构造法。作者先给了一个例子，这个例子可以很好地理解实际的证明过程。用自己的语言总结起来就是，先考虑第一层 $h_1$，利用定理 4，我们可以找到 $\left\lfloor\frac{n_{1}}{n_{0}}\right\rfloor$ 个区域 $R_i$，使得它们的激活 pattern 是 $\{1,2,...,n_0\},\{n_0+1,..., 2n_0\}....$ 它们互不相交。利用命题 5，可以找到对应的映射 $g_i$，将 $h_1(R_i)$ 映射到 $n_0$ 维空间中，且包含单位球。此后可以定义新的一个关于单位球的线性划分 $f'$，$f'$ 和 $[g_i,...]$ 的复合构成了第二层 $h_2$，类似的，$f'$ 的划分效果和 $h_1$ 一样，仍然可以找到 $\left\lfloor\frac{n_{2}}{n_{0}}\right\rfloor$ 个区域，激活 pattern 保持同样的性质。继续重复以上的过程，就会发现，每次都会将 $\left\lfloor\frac{n_{i}}{n_{0}}\right\rfloor$ 个区域的每一个都继续划分出 $\left\lfloor\frac{n_{i+1}}{n_{0}}\right\rfloor$ 个区域，最后一层的划分是 $\sum_{i=0}^{n_{0}}\left(\begin{array}{c}
> n_{k} \\
> i
> \end{array}\right)$ ，所以最终的划分数量至少是 $\left(\prod_{i=1}^{k-1}\left\lfloor\frac{n_{i}}{n_{0}}\right\rfloor\right) \sum_{i=0}^{n_{0}}\left(\begin{array}{c}
> n_{k} \\
> i
> \end{array}\right)$
>
> 证毕 。
>
> 注意这个构造方式和 Bounding and Counting Linear Regions of Deep Neural Networks 这篇文章在 $n_0 = 1$ 的相关命题中提到的 Z-pattern 函数构造法非常类似。

-----

#### 特殊的一类网络

这部分给出了当 $n_i = 2n_0$ 时的网络的空间划分数量的一个下界。同样是构造方法，也利用了命题 4 和 5.

结论：至少可以划分出 $2^{(k-1) n_{0}} \sum_{j=0}^{n_{0}}\left(\begin{array}{c}
2 n_{0} \\
j
\end{array}\right)$ 个区域。

----

> 证明思路：第一层的每两个神经元对应 $n_0$ 维中每一个维度上的绝对值，比如第一个神经元是第一个分量为正时的取值，第二个神经元时第一个分量为负时的绝对值。一个二维的例子是（这里先忽略 bias）
> $$
> \begin{aligned}
> &f_{1}(\mathbf{x})=\max \left\{0,[1,0]^{\top} \mathbf{x}\right\}\\
> &f_{2}(\mathbf{x})=\max \left\{0,[-1,0]^{\top} \mathbf{x}\right\}\\
> &f_{3}(\mathbf{x})=\max \left\{0,[0,1]^{\top} \mathbf{x}\right\}\\
> &f_{4}(\mathbf{x})=\max \left\{0,[0,-1]^{\top} \mathbf{x}\right\}
> \end{aligned}
> $$
> 这样每两个节点对应一个维度，一共可以划分出 $2^{n_0}$ 个区域。利用命题 5，可以设计出如下的 $g_i$:
> $$
> \left[\begin{array}{l}
> g_{1}(\mathbf{x}) \\
> g_{2}(\mathbf{x})
> \end{array}\right]=\left[\begin{array}{llll}
> 1 & 1 & 0 & 0 \\
> 0 & 0 & 1 & 1
> \end{array}\right]\left[\begin{array}{l}
> f_{1}(\mathbf{x}) \\
> f_{2}(\mathbf{x}) \\
> f_{3}(\mathbf{x}) \\
> f_{4}(\mathbf{x})
> \end{array}\right]=\left[\begin{array}{l}
> \operatorname{abs}\left(x_{1}\right) \\
> \operatorname{abs}\left(x_{2}\right)
> \end{array}\right]
> $$
> 这样就将每个象限映射到一个象限中。只要通过平移，就可以重复第一层的 $f$，将 $g$ 映射后的结果重新划分出 $2^{n_0}$ 个区域，由于此前不同象限映射到一个象限中，所以这次的划分效果相当于对 $2^{n_0}$ 的每个区域都划分出 $2^{n_0}$。和定理 1 一样的方式，可以构造出 $k-1$ 次如上的划分，因此最后的划分数量是
> $$
> 2^{(k-1) n_{0}} \sum_{j=0}^{n_{0}}\left(\begin{array}{c}
> 2 n_{0} \\
> j
> \end{array}\right)
> $$
> 证毕。

-----

#### 总结

这篇文章主要讨论的是下界，总体来说就是构造某种划分，使得可以划分出下界的数量。而这种构造方式似乎非常常见。其他的内容则是从下界中继续推出其他的一些结论，这部分就不要仔细讨论了。



