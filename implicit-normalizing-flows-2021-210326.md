# Implicit Normalizing Flows

[TOC]

## 信息

Authors: Cheng Lu, Jianfei Chen, Chongxuan Li, Qiuhao Wang and Jun Zhu

Year: ICLR 2021

Bibtex:

```
@article{lu2021implicit,
  title={Implicit Normalizing Flows},
  author={Lu, Cheng and Chen, Jianfei and Li, Chongxuan and Wang, Qiuhao and Zhu, Jun},
  journal={arXiv preprint arXiv:2103.09527},
  year={2021}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/implicit-normalizing-flows.pdf)

cite: 1



## Code

https://github.com/thu-ml/implicit-normalizing-flows



## 概括

这篇文章题为 implicit normalizing flows，含义就是 normalizing flow 模型的函数设计是以一个隐函数形式出现的，而通常的做法是显式形式。作者考虑的基础的模型是 ResFlow 模型，在这个模型中，函数形式是一个残差结构 f(x) = x + g(x)。基于这种函数形式，作者构造了一个隐式的函数形式 F(z, x) = gx(x) - gz(z) + x - z，并且证明了这种函数形式的表达能力更强。这两种形式都可以构成多个 block 的 stack 模型。ResFlow 好理解，对于 ImpFlow，这个隐式函数可以通过不动点计算构造出一个输入到输出的关系，所以也可以计算多个 block。因为基础模型是 ResFlow，而 ResFlow 的形式属于 Jacobian-free 的 flow 模型，所以在计算时，有很多技巧，非常复杂（这里的计算不仅指训练，也包括推断和采样）。在实验方面，作者有三个实验，一个是分类的实验，这个没看懂为什么 flow 模型可以做分类，一个是 2D 的 Toy data 的概率建模，还有一个是真实数据的概率建模，作者考虑的数据是 tabular dataset（这个不知道是什么），以及 CIFAR-10 和 5-bit CelebA（64x64）。

简单来说，这篇文章的主要贡献在于提供了另一种形式的 flow 模型，并且证明其表达能力的有效性，提供了计算上的细节。



## 方法

通常的 flow 模型：
$$
\begin{equation}
\ln p_{\mathbf{x}}(\mathbf{x})=\ln p_{\mathbf{z}}(f(\mathbf{x}))+\ln \left|\operatorname{det}\left(J_{f}(\mathbf{x})\right)\right|
\end{equation}
$$
并且要求 $f$ 可逆，Jacobian 矩阵的 log-determinant 可计算。后来出现的方法则放松了对 Jacobian 矩阵的要求。但仍然保持着一个显式的结构：
$$
\begin{equation}
\mathbf{z}=f(\mathbf{x})
\end{equation}
$$
这篇文章的方法基于 ResFlow，这个模型采用的是 free-form Jacobian 的模型。形式是：$\begin{equation}
f=f_{L} \circ \cdots \circ f_{1}
\end{equation}$，并且
$$
\begin{equation}
f_{l}(\mathbf{x})=\mathbf{x}+g_{l}(\mathbf{x}), \quad \operatorname{Lip}\left(g_{l}\right) \leq \kappa<1
\end{equation}
$$
限制了李普西茨常数。在作者提出的 ImpFlow 模型中，则进一步放松了对李普西茨常数的限制。

作者假设了一个隐函数的形式：
$$
\begin{equation}
F(\mathbf{z}, \mathbf{x})=\mathbf{0}, \text { where } F(\mathbf{z}, \mathbf{x})=g_{\mathbf{x}}(\mathbf{x})-g_{\mathbf{z}}(\mathbf{z})+\mathbf{x}-\mathbf{z}
\end{equation}
$$
并且 $\begin{equation}
\operatorname{Lip}\left(g_{\mathbf{x}}\right)<1
\end{equation}$, $\begin{equation}
\operatorname{Lip}\left(g_{\mathbf{z}}\right)<1
\end{equation}$.

可以证明在这个定义下，$F(z, x)$ 可以推出 z 和 x 之间的映射是可逆的（具有唯一的不动点）。从这个定义可以看出，ImpFlow 和 ResFlow 之间的密切关系，这个关系从下面的式子可以看出：
$$
\begin{equation}
\mathbf{z}=\left(\left(g_{\mathbf{z}}+\mathrm{Id}\right)^{-1} \circ\left(g_{\mathbf{x}}+\mathrm{Id}\right)\right)(\mathbf{x})
\end{equation}
$$
也就是说 ImpFlow 可以看成是两个 ResFlow 的模块的结合。

然后作者分析了 ImpFlow 和 ResFlow 的表达能力，设：
$$
\begin{equation}
\mathcal{R}:=\left\{f: f=g+\mathrm{Id}, g \in C^{1}\left(\mathbb{R}^{d}, \mathbb{R}^{d}\right), \operatorname{Lip}(g)<1\right\}
\end{equation}
$$

$$
\begin{equation}
\mathcal{R}_{\ell}:=\left\{f: f=f_{\ell} \circ \cdots \circ f_{1} \text { for some } f_{1}, \cdots, f_{\ell} \in \mathcal{R}\right\}
\end{equation}
$$

那么 ResFlow 模型的表达范围就是 $\mathcal{R}_{\ell}$。而一个 ImpFlow 模块的表达范围则是：
$$
\begin{equation}
\mathcal{I}=\left\{f: f=f_{2}^{-1} \circ f_{1} \text { for some } f_{1}, f_{2} \in \mathcal{R}\right\}
\end{equation}
$$
作者随后证明了
$$
\begin{equation}
\mathcal{R} \subsetneq \mathcal{R}_{2} \subsetneq \mathcal{I}
\end{equation}
$$
作者给出了可以用 $\mathcal{I}$ 表示但是无法用 $\mathcal{R}_2$ 表示的例子。但是也存在 $\mathcal{R}_3$ 可以表示的而 $\mathcal{I}$ 无法表示的函数。不过这个例子我并不关心。

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-26 下午4.39.46.png" alt="屏幕快照 2021-03-26 下午4.39.46" style="zoom:50%;" />

随后，作者给出了具体训练和推断等细节。需要注意的是因为 ImpFlow 模型基于 ResFlow 模型，所以很多做法参照了此前的结论。

在建模的时候，只要建模 $g_x, g_z$ 函数就可以了，不用特别考虑 $F$，因为 $F$ 只是一个计算上的工具。

第一个问题：推断过程

已知 $x$ 计算 $z$，这需要计算不动点，作者用 Broyden 方法迭代计算。这也意味着 ImpFlow 模型是可以有多个模块的，因为利用不动点计算就可以得到 x 和 z 之间的关系，而 (4) 式的定义中，x 和 z 也仅仅是输入和输出，不一定是信号和隐变量。

不过计算概率的式子是：
$$
\begin{equation}
\ln p(\mathbf{x})=\ln p(\mathbf{z})+\ln \operatorname{det}\left(I+J_{g_{\mathbf{x}}}(\mathbf{x})\right)-\ln \operatorname{det}\left(I+J_{g_{\mathbf{z}}}(\mathbf{z})\right)
\end{equation}
$$
但是后面两项很不好算，利用之前的方法，采用一个估计：
$$
\begin{equation}
\ln p(\mathbf{x})=\ln p(\mathbf{z})+\mathbb{E}_{n \sim p(N), \mathbf{v} \sim \mathcal{N}(0, I)}\left[\sum_{k=1}^{n} \frac{(-1)^{k+1}}{k} \frac{\left(\mathbf{v}^{T}\left[J_{g_{\mathbf{x}}}(\mathbf{x})^{k}\right] \mathbf{v}-\mathbf{v}^{T}\left[J_{g_{\mathbf{z}}}(\mathbf{z})^{k}\right] \mathbf{v}\right)}{\mathbb{P}(N \geq k)}\right]
\end{equation}
$$
文中说 $p(N)$ 是一个 distribution supported over the positive integers.

第二个问题：采样

同样是不动点的计算，采用 Broyden 方法。

第三个问题：训练

训练的目标函数是公式 (10)，即使考虑 (11) 式，也非常复杂，不过最后作者也给出了相应的计算，但是确实太复杂了。这里不详细展开。

最后，由于所有的 g 函数，都有李普西茨条件的限制，作者通过 spectral normalizatin 来控制这一点。不过怎么控制上界我有点不明白。



## 实验

在实验中，网络要建模的是 g 函数，作者在这一点上没有什么创新，我看了附录，感觉也比较简单，不过激活函数和normalizatin 层有一些不常见。

一共有三个实验，正如概括里写的那样。从实验的角度来说，这个方法仍然有很多限制。



## 反思

flow 模型这样的发展是合理的嘛？虽然模型的可逆性限制和 Jacobian 矩阵的限制对模型的表达能力有影响，但是从这篇文章看来，目前的研究思路让这个方法变得越来越复杂，如果要生成 1024x1024 这样的图像，在计算上完全不可能。



## 参考文献

