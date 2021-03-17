# Conditional Generative Modeling via Learning the Latent Space

[TOC]

## 信息

Authors: S Ramasinghe, K Ranasinghe, S Khan

Year: arXiv 2020

Bibtex:

```
@article{ramasinghe2020conditional,
  title={Conditional Generative Modeling via Learning the Latent Space},
  author={Ramasinghe, Sameera and Ranasinghe, Kanchana and Khan, Salman and Barnes, Nick and Gould, Stephen},
  journal={arXiv preprint arXiv:2010.03132},
  year={2020}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/conditional-generative-modeling-via-learning-the-latent-space.pdf)

cite: 1



## 概括

这篇文章研究的对象应该是conditional generative model，作者在这篇文章中提出的方法既不是 VAE，也不是 GAN，而且它要实现的目标是能够生成多种不同的输出，而不是确定性的输出。作者对 GAN 的模型和 VAE 的模型都提出了批评。特别是 GAN 模型，GAN 模型在这类问题上（图像缺失恢复）存在一个矛盾：重建损失和对抗损失是矛盾的。作者提出的方法应该称之为隐变量方法，有一些类似 VAE，但是对于隐变量的利用方式和训练手段都完全不同。作者用一个 encoder 对输入 x 进行编码，然后通过一个 Z 模块，结合一个随机的初始隐变量取值，通过循环的方式生成一个新的编码（可以看成是 h(x) 和 z 的一个函数），最后再通过解码器得到最终图像。encoder 和 decoder 之间存在 skip connection 连接，特别想一个 U-Net。

训练的时候，每个 sample 都会随机给一个隐变量初始值 z，然后迭代数步，更新 z，使得重建损失变小，然后用更新之后的 z 作为 label 训练 Z 模块，当作一个回归任务，输入是当前的 z 和 h(x)。最后固定 Z 模块，更新编码器和解码器。

推断的时候，先得到初步编码 h(x)，然后从一个随机的隐变量初始 z 出发，在 Z 模块上进行迭代，产生新的 z，然后和 h(x) 结合之后输入到解码器中得到最终结果。不同的隐变量初始值，对应不同的结果。

在这个方法中，Z 模块起的作用是从初始 z 到最终 z 的一个路径演变，训练的时候也是基于这个目的，所以才用了回归的形式。这种方法，我现在的理解是：它不追求隐变量和 x 之间有什么关系。它可以理解成存在两个编码，第一个编码是 h(x)，第二个编码是 h(x) 和 z 的融合。对于重建，后者表示了重建的信息。但是 z 如何获得呢，训练 Z 模块，从任意一个随机初始值出发，都可以沿着某一条路径到达一个合适的隐变量。因此不需要对隐变量有什么限制，只要某一个隐变量是合适的，那么用 Z 模块模拟出这个路径就可以了，这就是所谓 learning the latent space。



## 方法

这篇文章多次提到 multi-modal，实际上这里的 modal 是说真实的标签不止一个，或者说输出可以不是唯一的。为了引入输出的变化，作者用的方法就是引入隐变量，这个有一些类似于 VAE。

网络结构示意图。

![屏幕快照 2021-03-17 下午4.13.01](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-17 下午4.13.01.png)

为了避免在回归的时候陷入一种不动点的困境，作者设计的 Z 模块的更新可以用下式表达。其中 $r^{\prime}$ 和 $\rho$ 都是网络的一部分。
$$
\begin{equation}
z_{t+1}=z_{t}+\mathbb{E}\left[\rho\left(z_{t}, x_{j}\right)\right]\left[\frac{r^{\prime}\left(z_{t}, x_{j}\right)-z_{t}}{\left\|r^{\prime}\left(z_{t}, x_{j}\right)-z_{t}\right\|}\right]
\end{equation}
$$
训练算法的详细步骤：

![屏幕快照 2021-03-17 下午4.13.11](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-17 下午4.13.11.png)

可以看到，这里的对 Z 的更新，目的就是训练 Z 让它可以从任意初始 z 出发找到一个合适的隐变量。

训练和推断的示意图。注意到这里有两个 cost，一个是重建的，一个是隐变量的 z 回归。

![屏幕快照 2021-03-17 下午4.16.04](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-17 下午4.16.04.png)

最后有一些点：1. z 的采样是从球面上采样的。2. 作者还考虑李普西茨条件（但是我不太像管这个了）。

作者的实验很多，但是我总感觉不太可信。。。



## 反思

这篇文章提出的方法不同于以往的方法，感觉有点奇怪，但是又说不上是什么地方奇怪。这种对隐变量空间完全不控制的做法不好理解。如果把 Z 模块看成是一个单独的网络，就好像是根据 $h(x)$ 和初始随机 z 获得可以用于解码的新的编码。似乎可以理解成有问题的输入 $x$ 的编码到一个合理输出 $y$ 的编码之间的映射。这个想法我觉得可能才是值得借鉴的一个想法。