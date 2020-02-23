### Paper:

Learning Deep Energy Models: Contrastive Divergence vs. Amortized MLE

### Author:

Qiang Liu, Dilin Wang

### Year:

2017

### Notes:

这篇文章的主要目的是结合 contrastive divergence, Stein variational gradient descent, GAN, 等概念。提出了 SteinCD 和 SteinGAN，以及它们的结合。基于这几个模型，还考虑了条件生成的模型。

总的来说，需要优化的目标包含两个概念，一个是KL散度，一个是极大似然估计。作者认为基于能量的模型不一定能有效地捕捉低维流形，GAN从一定程度上克服了这一点，但是没有好的能量表示（概率表示）。

下面的笔记内容是：先说明SVGD、CD的概念以及它们的作用。然后叙述一个基于两者的基本框架，最后将该框架拓展到最终的模型。

**Stein Variational Gradient Descent (SVGD)**

假设我们有一些观测的点 $x_i$, 看成空间中的粒子，现在要移动它们使得，它们可以极小化关于概率 $p$ 的KL 散度。设粒子的运动为：
$$
x_{i}^{\prime} \leftarrow x_{i}+\epsilon \phi\left(x_{i}\right), \quad \forall i=1, \cdots, n
$$
$\phi\left(x_{i}\right)$ 表示的是方向。那么目标就是：
$$
\boldsymbol{\phi}^{*}=\underset{\boldsymbol{\phi} \in \mathcal{F}}{\arg \max }\left\{\mathrm{KL}\left(q_{0} \| p\right)-\mathrm{KL}\left(q_{[\epsilon \phi]} \| p\right)\right\}
$$
$q_0$ 是经验delta测度。当 $\epsilon \rightarrow 0$, $\phi^*\operatorname{arg} \max _{\boldsymbol{\phi} \in \mathcal{F}}\left\{-\frac{\mathrm{d}}{\mathrm{d} \epsilon} \mathrm{KL}\left(q_{[\epsilon \phi]} \| p\right)|_{\epsilon=0}\right\}$，当 $q_0 = p$ 时，导数为0. 如果考虑利用核函数构建的希尔伯特空间中寻找最优的 $\phi$,  其结果为：
$$
\begin{aligned}
&x_{i} \leftarrow x_{i}+\epsilon \phi^{*}\left(x_{i}\right), \quad \forall i=1, \ldots, n\\
&\phi^{*}\left(x_{i}\right)=\frac{1}{n} \sum_{i=1}^{n}\left[\nabla_{x_{j}} \log p\left(x_{j}\right) k\left(x_{j}, x_{i}\right)+\nabla_{x_{j}} k\left(x_{j}, x_{i}\right)\right]
\end{aligned}
$$
如果只有一个 $x_i$，那么上式就退化为了标准的极大化后验概率 $\log p(x)$. 

总之，SVGD 这一步的目的是可以得到一个更接近于 $p$ 分布的数据。（也许可以看成一个采样过程）。

**Contrastive Divergence**

对于一个能量模型而言，需要极大化似然函数，也就是假设 $p(x | \theta)=\frac{1}{Z(\theta)} \exp (f(x ; \theta))$，$Z(\theta)$ 是归一化常数。极大化似然函数得到似然函数关于 $\theta$ 的导数。
$$
\hat{\theta}=\underset{\theta}{\arg \max }\left\{L(\theta | X) \equiv \frac{1}{n} \sum_{i=1}^{n} \log p\left(x_{i} | \theta\right)\right\}\\\nabla_{\theta} L(\theta | X)=\mathbb{E}_{q_{0}}\left[\nabla_{\theta} f(x ; \theta)\right]-\mathbb{E}_{p_{\theta}}\left[\nabla_{\theta} f(x ; \theta)\right]
$$
这里的 $q_0$ 还是经验delta测度，也就是 $q_{0}(x)=\frac{1}{n} \sum_{i=1}^{n} \delta\left(x-x_{i}\right)$。极大化似然函数等价于 $\min _{\theta} \mathrm{KL}\left(q_{0} \| p_{\theta}\right)$，（也许这很困难，）作者考虑另一个目标函数 $\min _{\theta}\left\{\mathrm{CD}_{k} \stackrel{\text {def}}{=} \mathrm{KL}\left(q_{0} \| p_{\theta}\right)-\mathrm{KL}\left(q_{k} \| p_{\theta}\right)\right\}$ 这里的 $q_k$ 是将分布 $q_0$ 往 $p_{\theta}$ 移动了 $k$ 个 马尔可夫跳转，此时我们可以得到对应的极小化时 $\theta$ 的梯度：
$$
\theta \leftarrow \theta+\mu\left(\mathbb{E}_{q_{0}}\left[\nabla_{\theta} f(x ; \theta)\right]-\mathbb{E}_{q_{k}}\left[\nabla_{\theta} f(x ; \theta)\right]\right)
$$
可以比较(3)和(4)，两者的区别在于第二项，后者可以用负样本来计算。

CD 的本质和极大化似然函数是一样的，当 $k \rightarrow \infin$ 时，就可以看作是MLE的更新。但是只取 $k=1$ 的好处是，我们已经可以得到一个改善 $\theta$ 的很好的方向了。

**Stein Contrastive Divergence**

有了如上两个概念，作者结合两者，就提出了一个新的计算目标：
$$
\min _{\theta} \max _{\boldsymbol{\phi} \in \mathcal{F}}\left\{\frac{1}{\epsilon}\left(\mathrm{KL}\left(q_{0} \| p_{\theta}\right)-\mathrm{KL}\left(q_{[\epsilon \phi]} \| p_{\theta}\right)\right)\right\}
$$
max 一步相当于1个马尔可夫跳转，也就是计算 $p_{k}$, min 这一步相当于使得 $p_{\theta}$ 接近 $q_0$，极大化似然函数。由此得到 $\theta$ 的更新为：
$$
\theta \leftarrow \theta+\mu\left(\mathbb{E}_{q_{0}}\left[\nabla_{\theta} f(x ; \theta)\right]-\mathbb{E}_{q_{\left[\mathrm{c} \Phi^{*}\right]}}\left[\nabla_{\theta} f(x ; \theta)\right]\right)
$$
对应的算法就是：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-19%E4%B8%8B%E5%8D%889.30.51.png" style="zoom:50%;" />

在这个目标函数的基础上，如果做一些变形，也可以把更新改成：
$$
\theta \leftarrow \theta-\mu \mathbb{E}_{q_{0}}\left[\nabla_{\theta} \nabla_{x} f(x ; \theta) \phi^{*}(x)\right]\\
\begin{array}{l}
{\nabla_{\theta} \nabla_{x} f(x ; \theta) \phi^{*}(x)} \\
{\left.\approx-\frac{1}{\epsilon}\left[\nabla_{\theta} f(x ; \theta)-\nabla_{\theta} f\left(x+\epsilon \phi^{*}(x) ; \theta\right)\right)\right]}
\end{array} 或\\
\begin{array}{l}
{\nabla_{\theta} \nabla_{x} f(x ; \theta) \phi^{*}(x)} \\
{\approx-\frac{1}{2 \epsilon}\left(\left(\nabla_{\theta} f\left(x-\epsilon \phi^{*}(x) ; \theta\right)-\nabla_{\theta} f\left(x+\epsilon \phi^{*}(x) ; \theta\right)\right)\right.}
\end{array}
$$
 但是 (7) 的计算不如 (6) 简单。

**Amortized MLE**

尽管如此，作者考虑到用 SVGD 得到的采样结果不够真实，所以作者考虑用一个生成网络来生成图像，用它的结果替代 SVGD。所以作者考虑：
$$
\eta \leftarrow \underset{\eta}{\arg \min } \sum_{i=1}^{n}\left\|G\left(\xi_{i} ; \eta\right)-x_{i}-\epsilon \phi^{*}\left(x_{i}\right)\right\|_{2}^{2}
$$
这样就可以既结合生成网络，也结合了SVGD。对应的更新（也就是生成新的负样本的参数 $\eta$）为：
$$
\eta \leftarrow \eta+\epsilon \sum_{i} \partial_{\eta} G\left(\xi_{i} ; \eta\right) \phi^{*}\left(x_{i}\right)
$$
这样原来的目标函数就变成：
$$
\min _{\theta} \max _{\eta}\left\{\mathrm{KL}\left(q_{0} \| p_{\theta}\right)-\mathrm{KL}\left(q_{\left[G_{\eta}\right]} \| p_{\theta}\right)\right\}\\
\theta \leftarrow \theta+\mu\left(\mathbb{E}_{p_{0}}[\nabla f(x ; \theta)]-\mathbb{E}_{G_{\eta}}[\nabla f(x ; \theta)]\right)
$$
对应的算法就变成：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-19%E4%B8%8B%E5%8D%889.39.43.png" style="zoom:50%;" />

**其他**

有了新版本的更新框架，最后差的就是 $f(x;\theta)$ 的表达了。作者一开始用了限制玻尔兹曼机 $p(x, h)=\frac{1}{Z} \exp \left(\frac{1}{2} x^{\top} B h+b^{\top} x+c^{\top} h-\frac{1}{2}\|x\|_{2}^{2}\right)$。后来用考虑使用自编码的能量函数 $p(x | \theta) \propto \exp (-\|x-\mathrm{D}(\mathrm{E}(x ; \theta) ; \theta)\|)$. 

以及使用生成网络和不使用生成网络两种方法之间做一个权衡。也就是选择负样本的时候，以一定的概率选择其中一个模型生成的样本，以此来平衡两者的优缺点。

另外作者还提到如果结合条件，变成一个条件生成模型，还有训练上的技巧。从结果上说，和 DCGAN 的结果可比。

### Bibtex:

```latex
@article{liu2017learning,
  title={Learning deep energy models: Contrastive divergence vs. amortized mle},
  author={Liu, Qiang and Wang, Dilin},
  journal={arXiv preprint arXiv:1707.00797},
  year={2017}
}
```