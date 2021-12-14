# Noise2Self: Blind Denoising by Self-Supervision

[TOC]

## 信息

作者：Joshua Batson, Loic Royer

年份：2019

期刊会议：ICML 2019

Bibtex:

```latex
@inproceedings{batson2019noise2self,
  title={Noise2self: Blind denoising by self-supervision},
  author={Batson, Joshua and Royer, Loic},
  booktitle={International Conference on Machine Learning},
  pages={524--533},
  year={2019},
  organization={PMLR}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/noise2self-blind-denoising-by-self-supervision.pdf)

code: https://github.com/czbiohub/noise2self



## 概括

通读。

这篇文章研究的是自监督去噪的问题。这篇文章不考虑噪声的类型，使用的方法有些像 noise2nosie，但是仅仅使用一个噪声图片，而不是像 nosie2noise 那样使用一个噪声图片对。因为训练的基本方式和监督学习一样，为了避免学习出一个 indentity 的函数，作者的做法 noise2self 相当于改变了 input，target 仍然是噪声图片。具体来说，作者构造了 J-invariant 的概念，即预测某一个像素的子集时，不使用这个子集的信息来预测。然后将网络（本来一个网络是不具有 J-invariant 性质的）改造成一个具有 J-invariant 性质的网络。这样训练网络就是在 J-invariant 的约束下训练网络的。当然最后 inference 的时候，可以不用考虑 J-invariant，直接将噪声图片输入到网络中。

我们发现这篇文章是可以用定理一分析的。我们在方法的部分会给出更多的分析。



## 方法

这篇文章实际上包含的内容挺多的，但是我们这里仅仅记录我们所关心的部分，其余部分忽略。

**J-invariant** 的概念

我们尽量用简单的语言来表述。

定义：设 $\mathcal{J}$ 是一个维度的划分。实际上就是对所有像素划分成互不相交的子集。这些子集构成了集合 $\mathcal{J}$. 设 $\mathcal{J}$ 的一个元素 $J$，也就是一些像素的集合。如果函数 $f(x)_J$ 也就是在 $J$ 的区域的值和 $x_J$ 无关，那么就称为 $J$-invariant，如果对于任何 $J \in \mathcal{J}$ 都成立，就称为 $\mathcal{J}$-invariant.

通常，网络使用监督学习的方式训练的时候，对于网络所表示的函数 $f$ 没有什么限制。但是作者考虑在 $f$ 满足 $\mathcal{J}$-invariant 的性质前提下对网络进行训练，换言之，$f$ 必须是 $\mathcal{J}$-invaraint. 损失函数自然是：
$$
\mathcal{L}(f)=\mathbb{E}\|f(x)-x\|^{2}
$$
很显然 Identity 函数不是 $\mathcal{J}$-invariant 的。如果给定了 $\mathcal{J}$，(1) 训练出来的 optimal 函数 $f$ 有什么性质呢。

作者的结论（命题1）是：如果噪声图片 x 的噪声在任何一个 $J$ 区域都是独立于其补集 $J^c$，那么下式成立，
$$
\mathbb{E}\|f(x)-x\|^{2}=\mathbb{E}\|f(x)-y\|^{2}+\mathbb{E}\|x-y\|^{2} .
$$
这里的 y 是 clean image。

这个命题说明，优化 (1) 相当于是优化了监督学习，只不过 $f$ 只能是 $\mathcal{J}$-invariant 的。这也就意味着，不可能训练出和监督学习一样的函数 $f$，监督学习的最优结果，根据定理一，就是 $\mathbb{E}[y|x]$，但是 $f$ 的限制导致 optimal $f$ 不是它。那么是什么呢？

作者的结论（命题2）给出了答案：
$$
f_{\mathcal{J}}^{*}(x)_{J}=\mathbb{E}\left[y_{J} \mid x_{J^{c}}\right]
$$
实际上，命题2的结论也可以用定理一推出来。

> 我们这里对这个结论进行一些分析。如果 $\mathbb{E}\left[y_{J} \mid x_{J^{c}} \right] = \mathbb{E}\left[y_{J} \mid x \right]$，那么 optimal f 就是 $\mathbb{E}[y | x]$。但是这个假设不成立。所以 optimal f 和监督学习的 optimal f 有差异。这个差异有多大呢？
>
> 我们从另外一个角度来看这个问题，实际上 (1) 的训练等价于：
> $$
> \mathcal{L}(f)=\sum_{J \in \mathcal{J}} \mathbb{E}\left\|f_{J}\left(x_{J^{c}}\right)-x_{J}\right\|^{2}
> $$
> 由于 $x_J$ 可以看成是带 noise 的 $y_J$，所以，从 noise2noise 的角度，这个优化是等价于
> $$
> \mathcal{L}(f)=\sum_{J \in \mathcal{J}} \mathbb{E}\left\|f_{J}\left(x_{J^{c}}\right)- y_{J}\right\|^{2}
> $$
> 我们也可以把监督学习写成这个形式：
> $$
> \mathcal{L}(f)=\sum_{J \in \mathcal{J}} \mathbb{E}\left\|f_{J}\left(x\right)-y_{J}\right\|^{2}
> $$
> 这两个有什么区别？虽然作者已经分析了 $f_{\mathcal{J}}^{*}(x)_{J}=\mathbb{E}\left[y_{J} \mid x_{J^{c}}\right]$ ，但是我们很难分析 $\mathbb{E}\left[y_{J} \mid x_{J^{c}}\right]$ 和 $\mathbb{E}\left[y_{J} \mid x \right]$ 的差异是什么。换一个角度。我们把监督学习的网络的输入定义为 $X= \left\{x, x, x, ..., x \right\}_{J}$，noise2self 的输入定义为 $X_J = \left\{(x_{J^c}, s(x_{J^c})), ..., \right\}_J$，输出都是相同的 $y$，那么定理一的结果告诉我们，监督学习的结果是 $E[y|X]$，noise2self 是 $E[y|X_J]$，我们只要分析 $X$ 和 $X_J$ 的分布的差异就可以了。
>
> 首先，我们需要指定 $s$  大致具有什么性质。$s$ 就是用 $s(x_{J^c})$ 来填补了 $x_J$ 的像素值。假如 $s$ 的选择是随机的像素值，很显然，这相当于 $X_J$ 是 $X$ 的一个噪声版本（只是噪声施加的位置比较奇特）。那么噪声化的 $X_J$ 具有更多的不确定性，理论上 noise2self 的效果是要弱于监督学习的。如果 $s$ 是neibor 像素的均值，这引入的噪声大小其实难以分析，因为 $X$ 是 $Y$ 的噪声版本，如果 $s$ 是均值，说不定会减弱 $X$ 在 $Y$ 上的噪声，这个似乎可以用大量的数据的实际情况来分析。如果这个数据集，能够减弱噪声，也许反而效果是一种提升（这里忽略 noise2noise 的效应）。如果这个数据集不是减弱噪声，那么就和随机像素值一样的分析，应该是效果变差了。

接下来，我们考虑一个问题，如果构造满足 $\mathcal{J}$-invariant 的函数。实际上，通过下面的式子，构造出来的就一定是 $\mathcal{J}$-invariant 的。这里的 $s(x)$ 就是一个替代函数，将 $J$ 区域的像素值替换成和原来无关的一个值。常见的 $\mathcal{J}$ 会是每个像素构成一个 $J$，此时 $s$ 可以是一个随机值，也可以是周围像素的均值。
$$
f_{\theta}(x)_{J}:=g_{\theta}\left(\mathbf{1}_{J} \cdot s(x)+\mathbf{1}_{J^{c}} \cdot x\right)_{J}
$$
**如何改进的更好**

由于 $f$ 是 J-invariant，因此只用 $f(x)$ 得到的某个位置的像素 $f(x)_j$ 是不包含 $x_j$ 的信息。一种改进的做法在这个去噪结果和输入的噪声结果做一个加权平均：
$$
\lambda f(x)_{j}+(1-\lambda) x_{j}
$$
$\lambda$ 的选择是噪声的 variance 大小除以自监督训练的 loss。（但是我没理解为什么，也没有细想）。



## 实验

我们这里仅仅讨论深度学习训练的部分，其余内容忽略。

作者使用了的数据集是 Hanzi, Imagenet 和 CellNet。噪声比较复杂，因为这篇文章并没有指定噪声应该是什么类型的。对于前两个数据集，用的是泊松、高斯和伯努利的混合噪声，对于最后一个数据集，用的模拟的 sCMOS  相机噪声。

我们最关心的是作者如何选择 J 和训练网络。作者选择的网络结构有 U-Net 和 DnCNN。作者说 J 的选择 random partition of 25 subsets，然后改造成一个 J-invariant 的网络。这个部分实际上写的很不清楚。但是作者给代码了。我认为要搞清楚，还是得看代码。不过这篇文章的实验结果我认为可以不用在意，通常大家不使用这些数据集的。

关于 inference，假设原来的网络是 $g$，改造成 J-invariant 之后是 $f$，那么训练的时候用 $f$ 来训练的，但是测试的时候，用 $g$ 来测试，效果更好一点。用 $f$ 来测试其实很麻烦。

作者也提到，这个方法也可以用来训练一张噪声图片。

实验结果表明，在相同的网络结构下（U-Net），竟然有的时候比监督学习和 noise2noise 更强。这非常让我惊讶。甚至 noise2noise 都有比监督学习的更好，我认为这是不太可能的。我感到非常奇怪。当然如果使用 DnCNN 的结构，监督学习优于noise2noise 优于 noise2self。我认为这是合理的。比较奇怪的是其他方法都是 DnCNN 的效果好于 U-Net，但是在 hanzi 数据集上，noise2self 反而是 U-Net 更好，很奇怪。



## 参考文献

Murphy, K. P. Machine Learning: a Probabilistic Perspective. Adaptive computation and machine learning series. MIT Press, Cambridge, MA, 2012. ISBN 978-0-26201802-9. 这篇文章提出了去噪任务下的定理一的一些结论。定理一的论文应该要引用。