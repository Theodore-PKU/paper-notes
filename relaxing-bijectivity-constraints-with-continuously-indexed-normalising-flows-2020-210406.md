# Relaxing Bijectivity Constraints with Continuously Indexed Normalising Flows

[TOC]

## 信息

Authors: Rob Cornish, Anthony Caterini, George Deligiannidis, Arnaud Doucet

Year: ICML 2020

Bibtex:

```
@inproceedings{cornish2020relaxing,
  title={Relaxing bijectivity constraints with continuously indexed normalising flows},
  author={Cornish, Rob and Caterini, Anthony and Deligiannidis, George and Doucet, Arnaud},
  booktitle={International Conference on Machine Learning},
  pages={2133--2143},
  year={2020},
  organization={PMLR}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/relaxing-bijectivity-constraints-with-continuously-indexed-normalising-flows.pdf)

cite: 14



## 概括

这篇文章是对 flow 模型思路上的一个比较重大的改进，目前还不清楚是否非常重要。flow 模型是一个双射映射，这在拓扑上有一个重要的局限，那就是隐变量的拓扑结构和 x 的拓扑结构必须保持一致。然而在实际中，x 的拓扑结构基本无法和隐变量相同。作者给了一个 bi-Lipschitz 的概念，只有当这个常数是无穷大的时候，才允许 x 和 z 之间是一个双射。作者以 resflow 模型为例，指出在 flow 模型中，这往往是无法实现的，要么计算量太大（比如层数很多），要么表达能力不够（bi-Lip 不够大），这是一个矛盾。作者为了规避 flow 模型的这个缺陷，提出引入一个连续的 index u，来构建 flow 模型，叫做 CIF。这个方法此前也有人尝试，但是他们使用了离散的 index，这篇文章则使用了连续的 index。简单概括起来，这个 u 就是一个额外的变量，只有给定 u 之后，x 和 z 之间的函数才被确定下来。在文章中，u 实际上是 conditon on z 的，也是一个随机变量。在方法部分会更详细地给出相关的公式（没有公式，很难说清楚）。

但是这个方法也带来了一个缺陷，那就是无法像原来的 flow 模型那样快速计算 x 的概率。为了解决这一个问题，作者提出了一个类似于 VAE 损失函数的 ELBO 下界，用这个替代值来计算。为此，需要有一个 u condition on z 的模块（这个模块和前述的 condition on z 不同）。但是这模块具体是什么结构，作者似乎没有说明。

实验方面，除了图像数据，还有其他数据类型。图像方面，作者考虑的仍然是比较简单的数据集 MNIST 和 CIFAR-10。作者提出的 CIF 是一个形式上的改动，可以适用于任何具体的 flow 模型。作者使用的 flow 模型还是比较简单的 real NVP。

作者分析了 CIF 的优缺点，我主要不明白的是为什么这个形式可以实现所谓的 bi-Lip 无穷大或者说规避了拓扑结构不同的问题。另外，作者还和 VAE 的形式进行了对比，指出 VAE 也可以看成是一种特例。不过在这个方法中，u 和 z 的含义是什么？我觉得需要继续思考，特别是 u 和 VAE 之间的隐变量是平行的概念，而不是 z 和 VAE 的隐变量是平行的概念，我觉得非常神奇。

最后，关于 u 的概念，我觉得和图像表示有着极大的联系，也许这篇文章提供了新的 framework 的一个很好的理论依据。



## 方法

这个部分对 CIF 方法的具体细节进行说明。

设 $\mathcal{U}$ 是一组 index（连续值），函数 $\{F(\cdot; u)\}_{u \in \mathcal{U}}$ 是一组根据 $u$ 决定的双射函数 (z to x)。整个计算过程可以描述为：
$$
Z \sim P_{Z}, \quad U \sim P_{U \mid Z}(\cdot \mid Z), \quad X:=F(Z ; U)
$$
当 Z 具有 multi-level 时，可以表示为：
$$
Z_{0} \sim P_{Z_{0}}, U_{\ell} \sim P_{U_{\ell} \mid Z_{\ell-1}}\left(\cdot \mid Z_{\ell-1}\right), Z_{\ell}:=F_{\ell}\left(Z_{\ell-1} ; U_{\ell}\right)
$$
这里 $Z_L = X$，并且 $F, U|Z$ 的形式是（这里忽略了下标）：
$$
\begin{aligned}
F(z ; u) &=f\left(e^{-s(u)} \odot z-t(u)\right) \\
p_{U \mid Z}(\cdot \mid z) &=\operatorname{Normal}\left(\mu^{p}(z), \Sigma^{p}(z)\right)
\end{aligned}
$$
$f$ 可以是任意一个双射函数（对应任意的 flow 模型框架）。实际上 U|Z 的模型可以更复杂。只要是一个分布就可以了。

由于引入了 $U$，计算 x 的概率时就变得复杂一些。联合概率可以写成：
$$
p_{X, U, Z}(x, u, z):=p_{Z}(z) p_{U \mid Z}(u \mid z) \delta(x-F(z ; u))
$$
X 和 U 的联合概率则可以写成：
$$
\begin{aligned}
p_{X, U}(x, u) &:=p_{Z}\left(F^{-1}(x ; u)\right) \\
\quad \times & p_{U \mid Z}\left(u \mid F^{-1}(x ; u)\right)\left|\operatorname{det} \mathrm{D} F^{-1}(x ; u)\right| .
\end{aligned}
$$
如果 Z 是 L-level，那么每一级的 $Z_{\ell}$ 就是：
$$
\begin{array}{l}
p_{Z_{\ell}, U_{1: \ell}}\left(z_{\ell}, u_{1: \ell}\right):=p_{Z_{\ell-1}, U_{1: \ell-1}}\left(F_{\ell}^{-1}\left(z_{\ell} ; u_{\ell}\right), u_{1: \ell-1}\right) \\
\times p_{U_{\ell} \mid Z_{\ell-1}}\left(u_{\ell} \mid F_{\ell}^{-1}\left(z_{\ell} ; u_{\ell}\right)\right)\left|\operatorname{det} \mathrm{D} F_{\ell}^{-1}\left(z_{\ell} ; u_{\ell}\right)\right|
\end{array}
$$
一般来说，此时 x 的概率，利用积分形式，就得到。
$$
p_{X}(x):=\int p_{X, U_{1: L}}\left(x, u_{1: L}\right) \mathrm{d} u_{1: L}
$$
从 (5) 可以看出，x 的概率计算变得复杂了，因为不再是只和 z 有关的简单式子。积分计算是不可能用来训练的，就类似于 VAE 一样。所以作者提出了 ELBO 的下界。假设有一个估计的概率模型：
$$
q_{U_{1: L} \mid X} \approx p_{U_{1: L} \mid X}
$$
那么我们有：
$$
\mathcal{L}(x):=\mathbb{E}_{u_{1: L} \sim q_{U_{1: L} \mid X}(\cdot \mid x)}\left[\log \frac{p_{X, U_{1: L}}\left(x, u_{1: L}\right)}{q_{U_{1: L} \mid X}\left(u_{1: L} \mid x\right)}\right]\\
\mathcal{L}(x)  \leq \log p_L(x)
$$
那么训练的时候就用 $\sum\mathcal{L}(x)$ 来训练。但是如何建模 $q_{U|X}$? 作者给出了一个比较巧妙的办法。
$$
q_{U_{1: L} \mid X}\left(u_{1: L} \mid x\right):=\prod_{\ell=1}^{L} q_{U_{\ell} \mid Z_{\ell}}\left(u_{\ell} \mid z_{\ell}\right)
$$
这样就将 $u$ 和 $z$ 联系起来。实际上此前的 $u_\ell$ 是关于 $z_{\ell -1}$，而这里变成和 $z_{\ell}$ 有关。这个形式非常类似 VAE。因为这又是一个概率模型，作者使用了重参数的技巧。实际的形式是
$$
q_{U_{\ell} \mid Z_{\ell}}\left(\cdot \mid z_{\ell}\right)=\operatorname{Normal}\left(\mu_{\ell}^{q}\left(z_{\ell}\right), \Sigma_{\ell}^{q}\left(z_{\ell}\right)\right)
$$
当给定 x，计算其概率的时候，利用重要性采样，可以计算多个 $\mathcal{L}(x)$ 的采样值，然后用下式来估计
$$
m^{-1} \log \operatorname{SumExp}\left(\hat{\mathcal{L}}^{(1)}, \ldots, \hat{\mathcal{L}}^{(m)}\right) \rightarrow \log p_{X}(x)
$$
整个训练算法的形式是：

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-06 下午8.08.21.png" alt="屏幕快照 2021-04-06 下午8.08.21" style="zoom:50%;" />

而对数行列式的计算则是：
$$
\log \left|\operatorname{det} D f_{\ell}^{-1}\left(e^{s_{\ell}\left(u_{\ell}\right)} \odot\left(z_{\ell}+t_{\ell}\left(u_{\ell}\right)\right)\right)\right|+\sum_{i=1}^{d}\left[s_{\ell}\left(u_{\ell}\right)\right]_{i}
$$
以上就是整个 CIF 的具体细节。



## 反思

这篇文章为了解决 flow 模型的拓扑结构问题，引入了新的变量 U，而 U 在这里起到的作用我感觉和图像的表示有关系。但是 U 的引入带来了计算的麻烦。作者用类似 VAE 的方法来处理，不知道有没有更好的方式，特别是能否直接从 $z$ 计算出 u 呢？我感觉 $u_\ell$  和 $z_\ell$ 的关系应该值得进一步思考。这篇文章的形式我觉得非常重要，应当值得进一步思考。甚至有可能基于这篇文章来设计模型。



## 参考文件

Behrmann, J., Vicol, P., Wang, K.-C., Grosse, R. B., and Jacobsen, J.-H. On the invertibility of invertible neural networks, 2020.

Jaini, P., Selby, K. A., and Yu, Y. Sum-of-squares polynomial ﬂow. In International Conference on Machine Learning, pp. 3009–3018, 2019.