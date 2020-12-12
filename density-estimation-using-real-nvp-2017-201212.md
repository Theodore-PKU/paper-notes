# Density estimation using real nvp

[TOC]

### 信息

Authors: Laurent Dinh, Jascha Sohl-Dickstein, Samy Bengio

University of Montreal and Google Brain

ICLR 2017

```latex
@article{dinh2016density,
  title={Density estimation using real nvp},
  author={Dinh, Laurent and Sohl-Dickstein, Jascha and Bengio, Samy},
  journal={arXiv preprint arXiv:1605.08803},
  year={2016}
}
```

引用量 900+

 [Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/density-estimation-using-real-nvp.pdf)



### 概括

这篇文章是 NICE 模型的续作。基本思路和 NICE 模型一样，但是改变了函数变换的形式。变换函数采用 scale + bias 的形式，其中应用了残差网络。另外也改变了网络的结构和一些细节，考虑了多尺度、不同的分量拆分方式、增加了隐变量的层级。整体仍然是一个利用多层可逆变换实现对高维数据的分布建模。

这个模型称为 real-valued non-volume preserving (real NVP) transformation，non-volume preserving 是因为变换的 Jacobian 矩阵不是常数。



### 思路

#### 模型背景

核心的原理仍然是概率变量替换公式。
$$
\begin{align}p_{X}(x) &=p_{Z}(f(x))\left|\operatorname{det}\left(\frac{\partial f(x)}{\partial x^{T}}\right)\right| \\
\log \left(p_{X}(x)\right) &=\log \left(p_{Z}(f(x))\right)+\log \left(\left|\operatorname{det}\left(\frac{\partial f(x)}{\partial x^{T}}\right)\right|\right)
\end{align}
$$
当 $f: X \rightarrow Z$ 建立之后，可以用下式得到 $x$ 的概率
$$
p_{X}(x)=p_{Z}(z)\left|\operatorname{det}\left(\frac{\partial g(z)}{\partial z^{T}}\right)\right|^{-1}
$$



#### Coupling layers

和 NICE 模型不同，这篇文章采用的变换函数形式是：
$$
\begin{align}
y_{1: d} &=x_{1: d} \\
y_{d+1: D} &=x_{d+1: D} \odot \exp \left(s\left(x_{1: d}\right)\right)+t\left(x_{1: d}\right)
\end{align}
$$
逆变换则是：
$$
\begin{align}
x_{1: d} &=y_{1: d} \\
x_{d+1: D} & =\left(y_{d+1: D}-t\left(y_{1: d}\right)\right) \odot \exp \left(-s\left(y_{1: d}\right)\right)
\end{align}
$$
如果用一个二值 mask，也可以写成：
$$
y=b \odot x+(1-b) \odot(x \odot \exp (s(b \odot x))+t(b \odot x))
$$
虽然函数形式采用了 $s, t$ 分别对应 scale 和 bias，但是基本形式仍然是变量拆分成两个部分，以此达到 Jacobian 行列式便于计算的目的，计算结果是 $\exp \left[\sum_{j} s\left(x_{1: d}\right)_{j}\right]$。

$s, t$ 的形式是 rectiﬁed convolutional networks (deep residual network). 但没有给具体的网络结构。

#### 变量拆分方法

有两种，一个是 spatial checkboard，一个是 channel-wise。如下图所示

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-12 下午2.52.37.png" alt="屏幕快照 2020-12-12 下午2.52.37" style="zoom:50%;" />

同时这个图也表示了 squeeze 实现分辨率下降的的方式。

因为一个 cupling layer 有一部分分量是没有改变的，所以采用交替的方式实现所有分量的更新。

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-12 下午2.55.12.png" alt="屏幕快照 2020-12-12 下午2.55.12" style="zoom:50%;" />

#### 模型结构

和 NICE 模型不同，real NVP 模型采用了多尺度的结构，因此需要下采样。下采样是用 squeeze 的形式实现的。每个尺度的具体形式是：3 个 spatial checkboard 交替层 + squeeze 层 + 3 个 channel-wise 交替层。最后一个 scale 包含四个 checkboard 交替层。

每个 scale 最后的输出，只有一半的分量会传递到下一个 scale，而不传递的部分则是当作隐变量的一部分 $z^{(i)}$，最后一个 scale 则不需要分离。因此实际的网络结构为：
$$
\begin{align}
h^{(0)} &=x \\
\left(z^{(i+1)}, h^{(i+1)}\right) &=f^{(i+1)}\left(h^{(i)}\right) \\
z^{(L)} &=f^{(L)}\left(h^{(L-1)}\right) \\
z &=\left(z^{(1)}, \ldots, z^{(L)}\right)
\end{align}
$$
图示：

![屏幕快照 2020-12-12 下午2.55.29](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-12 下午2.55.29.png)

这里的 $z_1, z_2$ 实际是第一个 scale 分离的分量。

隐变量的分布选择，作者采用了标准高斯分布。但是也提到可以用其他分布甚至是学到的分布，但是就不知道怎么处理了。

不过我好奇的是，为什么训练出来的隐变量分布就是高斯呢？

#### BN 层

模型中 $s, t$ 这些网络中的 BN 层仅仅是一个 rescale 函数：
$$
\begin{equation}
x \mapsto \frac{x-\tilde{\mu}}{\sqrt{\tilde{\sigma}^{2}+\epsilon}}
\end{equation}
$$
BN 层的参数使用移动平均值：
$$
\begin{equation}
\begin{array}{l}
\tilde{\mu}_{t+1}=\rho \tilde{\mu}_{t}+(1-\rho) \hat{\mu}_{t} \\
\tilde{\sigma}_{t+1}^{2}=\rho \tilde{\sigma}_{t}^{2}+(1-\rho) \hat{\sigma}_{t}^{2}
\end{array}
\end{equation}
$$
注意，这里求梯度的时候只考虑 $\hat{\mu}_t, \hat{\sigma}_t$

同时这个 BN 层也用于 coupling layer output，所以 BN 层也需要计算 Jacobian 行列式。当然这个计算很简单：$\begin{equation}
\left(\prod_{i}\left(\tilde{\sigma}_{i}^{2}+\epsilon\right)\right)^{-\frac{1}{2}}
\end{equation}$



Real NVP 实现的二维分布变换的例子：

![屏幕快照 2020-12-12 下午2.20.32](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-12 下午2.20.32.png)

### 实验

由于一般图像的 value 值是 [0, 256]，作者对像素值进行了处理，减少边界效应，因此做了一个变换 $\begin{equation}
\operatorname{logit}\left(\alpha+(1-\alpha) \odot \frac{x}{256}\right)
\end{equation}$，这里的 $\alpha = 0.5$.

实验用的数据分辨率都比较小。

作者尝试了用训练好的模型生成更大分辨率的图像，但是明显看出整个图像的布局是有很大问题的。

> 这其实是一个很重要的问题。图像的分布到底有什么样的特点。

作者还考虑带条件的模型，但不知道具体怎么实现的。（也许是不同的图像类别用不同的参数？）

### 其他方法及比较

在文献调研中，作者对过去的工作进行了分类，其中有一篇文章给出了 ICA 的一个理论结果。[29] Aapo Hyvärinen and Petteri Pajunen. Nonlinear independent component analysis: Existence and uniqueness results. Neural Networks, 12(3):429–439, 1999. 我感觉比较重要。

#### 概率无向图 probabilistic undirected graphs

Restricted Boltzmann Machines

[58] Paul Smolensky. Information processing in dynamical systems: Foundations of harmony theory. Technical report, DTIC Document, 1986.

Deep Boltzmann Machines

[53] Ruslan Salakhutdinov and Geoffrey E Hinton. Deep boltzmann machines. In International conference on artiﬁcial intelligence and statistics, pages 448–455, 2009.

#### 有向图模型

VAE

[35] Diederik P Kingma and Max Welling. Auto-encoding variational bayes. arXiv preprint arXiv:1312.6114, 2013.

[49] Danilo Jimenez Rezende, Shakir Mohamed, and Daan Wierstra. Stochastic backpropagation and approximate inference in deep generative models. arXiv preprint arXiv:1401.4082, 2014.

近期 work

[42] Lars Maaløe, Casper Kaae Sønderby, Søren Kaae Sønderby, and Ole Winther. Auxiliary deep generative models. arXiv preprint arXiv:1602.05473, 2016.

[48] Danilo Jimenez Rezende and Shakir Mohamed. Variational inference with normalizing ﬂows. arXiv preprint arXiv:1505.05770, 2015.

[55] Tim Salimans, Diederik P Kingma, and Max Welling. Markov chain monte carlo and variational inference: Bridging the gap. arXiv preprint arXiv:1410.6460, 2014.

[63] Dustin Tran, Rajesh Ranganath, and David M Blei. Variational gaussian process. arXiv preprint arXiv:1511.06499, 2015.

[10] Yuri Burda, Roger Grosse, and Ruslan Salakhutdinov. Importance weighted autoencoders. arXiv preprint arXiv:1509.00519, 2015.

[59] Jascha Sohl-Dickstein, Eric A. Weiss, Niru Maheswaranathan, and Surya Ganguli. Deep unsupervised learning using nonequilibrium thermodynamics. In Proceedings of the 32nd International Conference on Machine Learning, ICML 2015, Lille, France, 6-11 July 2015, pages 2256–2265, 2015.

[34] Diederik P Kingma, Tim Salimans, and Max Welling. Improving variational inference with inverse autoregressive ﬂow. arXiv preprint arXiv:1606.04934, 2016.

[50] Oren Rippel and Ryan Prescott Adams. High-dimensional probability estimation with deep density models. arXiv preprint arXiv:1302.5125, 2013.

[3] Johannes Ballé, Valero Laparra, and Eero P Simoncelli. Density modeling of images using a generalized normalization transformation. arXiv preprint arXiv:1511.06281, 2015.

[17] Laurent Dinh, David Krueger, and Yoshua Bengio. Nice: non-linear independent components estimation. arXiv preprint arXiv:1410.8516, 2014.

[29] Aapo Hyvärinen and Petteri Pajunen. Nonlinear independent component analysis: Existence and uniqueness results. Neural Networks, 12(3):429–439, 1999.

#### 自回归模型

[61] Lucas Theis and Matthias Bethge. Generative image modeling using spatial lstms. In Advances in Neural Information Processing Systems, pages 1918–1926, 2015.

[46] Aaron van den Oord, Nal Kalchbrenner, and Koray Kavukcuoglu. Pixel recurrent neural networks. arXiv preprint arXiv:1601.06759, 2016.

[66] Oriol Vinyals, Samy Bengio, and Manjunath Kudlur. Order matters: Sequence to sequence for sets. arXiv preprint arXiv:1511.06391, 2015.

#### GAN模型

[21] Ian J. Goodfellow, Jean Pouget-Abadie, Mehdi Mirza, Bing Xu, David Warde-Farley, Sherjil Ozair, Aaron C. Courville, and Yoshua Bengio. Generative adversarial nets. In Advances in Neural Information Processing Systems 27: Annual Conference on Neural Information Processing Systems 2014, December 8-13 2014, Montreal, Quebec, Canada, pages 2672–2680, 2014.

[15] Emily L. Denton, Soumith Chintala, Arthur Szlam, and Rob Fergus. Deep generative image models using a laplacian pyramid of adversarial networks. In Advances in Neural Information Processing Systems 28:Annual Conference on Neural Information Processing Systems 2015, December 7-12, 2015, Montreal, Quebec, Canada, pages 1486–1494, 2015.

[47] Alec Radford, Luke Metz, and Soumith Chintala. Unsupervised representation learning with deep convolutional generative adversarial networks. CoRR, abs/1511.06434, 2015.

#### 和其他模型的比较

在最后的讨论部分，作者将 real NVP 模型和 GAN、自回归、VAE 进行了比较。

1. 该模型和自回归模型一样，可以计算准确的似然函数值
2. 和 GAN、VAE 一样，可以有灵活复杂的函数变换形式。
3. 不需要 reconstruction cost
4. 和 GAN、VAE 不一样，latent space 有语义。**（但是这一点我完全没有看出来）**