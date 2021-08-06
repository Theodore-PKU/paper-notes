# Residual Flows for Invertible Generative Modeling

[TOC]

## 信息

Authors: Ricky T. Q. Chen, Jens Behrmann, David Duvenaud, Jörn-Henrik Jacobsen

Year: NeurIPS 2019

```latex
@article{chen2019residual,
  title={Residual flows for invertible generative modeling},
  author={Chen, Ricky TQ and Behrmann, Jens and Duvenaud, David and Jacobsen, J{\"o}rn-Henrik},
  journal={arXiv preprint arXiv:1906.02735},
  year={2019}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/residual-flows-for-invertible-generative-modeling.pdf)



## 概括

这篇文章提出了 Residual Flow 模型，该模型对于网络结构的限制很少，但对于 Lipschitz 条件有一定的要求。作者表示，此前的相关工作在 density 的估计上是基于有偏估计，而且随着网络的表达能力的增长，bias 会越大。这篇文章则利用 'Russian roulette' 提出了一个新的 log density 的估计，并且利用梯度的无穷级数来计算（这个方法之前以前用到了，这篇文章只是提供了一个无偏估计的方法，不需要计算整个级数的和，这是核心内容）。作者还提出了两个技巧，减少训练过程中的内存。ResFlow 模型在概率估计方面取得了 SOTA 的效果，比其他的 flow-based 模型更好。不过生成质量我认为还是很一般的。



## 方法

Behrmann 等人此前提出的 invertible residual network 利用了 Lipschitz 的限制，可以实现使用判别深度网络的结构对 flow-based 模型进行建模。但是他们的方法在概率密度的估计上需要使用无穷级数，然后有限的计算会导致较大的偏执。这导致估计是不准确的。这篇文章则着重改进了这一点，提出了无偏的 density 估计。

### 极大似然估计

假设建模的分布是 $p_{\theta}$，数据是 $p_{data}$，那么两者的 KL 散度的梯度等于 $p_{\theta}$ 的对数概率的梯度的期望值。
$$
\nabla_{\theta} D_{\mathrm{KL}}\left(p_{\text {data }} \| p_{\theta}\right)=\nabla_{\theta} \mathbb{E}_{x \sim p_{\text {data }}(x)}\left[\log p_{\theta}(x)\right]=\mathbb{E}_{x \sim p_{\text {data }}(x)}\left[\nabla_{\theta} \log p_{\theta}(x)\right]
$$
变换替换公式：
$$
\log p(x)=\log p(f(x))+\log \left|\operatorname{det} \frac{d f(x)}{d x}\right|
$$

#### Invertible residual networks

由简单的形式组成：$y = f(x) = x + g(x)$，这个变换是可逆的，利用 Banach 不动点定理，条件是 $g$ 必须是 Lipschitz 常数有限制（但是这个限制具体是啥？）在这里的概率变换计算，利用的是一个级数求和：
$$
\log p(x)=\log p(f(x))+\operatorname{tr}\left(\sum_{k=1}^{\infty} \frac{(-1)^{k+1}}{k}\left[J_{g}(x)\right]^{k}\right)
$$
$J_g(x)$ 是 g 关于 x 的梯度。而这里还要计算 Trace，所以 Skilling-Hutchinson 估计也会被利用上，来估计 power series 的 Trace。Behrmann 用的是固定的有限级数作为无穷级数的和的估计。但是这引入了 bias，并且当 x 和 g 的 Lipschitz 常数变大的时候，bias 会增加，而且，也影响了无穷级数的收敛性。（这是一个很大的局限，因为如果 bias 很小，网络表达能力不行，这样的矛盾会导致无法用于高维的数据）

### Residual Flows

**无偏的对数概率估计**

这个想法仍然是要计算无穷级数，只不过并不是每次计算都要算无穷级数的所有项，而是以一定的概率来决定是否要计算后面的项。这个过程简单来说就是，当计算完前一项，抛一个硬币，判断是否要计算下一项。另一种解释可以看成是每次计算前，先抽样出这次要计算多少项，然后再以一定的权重进行求和。
$$
\sum_{k=1}^{\infty} \Delta_{k}=\mathbb{E}_{n \sim p(N)}\left[\sum_{k=1}^{n} \frac{\Delta_{k}}{\mathbb{P}(N \geq k)}\right]
$$
$\Delta_k$ 表示级数的每一项。很显然，当 k 越大时，N >= k 的概率越小，所以对应的权重越大。

但是，上述的式子要成立也是有限制条件的。

![屏幕快照 2021-08-06 下午4.43.24](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-06 下午4.43.24.png)

定理1给出了条件。这里已经用上了 Skilling-Hutchinson 估计来计算 Trace（也就是级数的每一项）

作者表示，不需要对 p(N) 进行调准，作者直接取 p(N) = Geom(0.5)，首先无穷级数的前两项是一定会计算的，p(N) 只是用来作为后续项的估计。这样的好处是，平均只要计算 4 项就可以了。而 Behrmann 会算更多的项。

这个估计是这篇文章的核心，代价是有的时候需要计算的项比较多，因此对于内存也是有要求的。另外 Lipschitz 条件也需要满足。

### 减少内存

作者提出了两种手段减少内存。假设没有任何技巧，内存量是 O(mn)，n 指的是需要计算的级数的项，m 指的是整个网络的 residual block 的数量。

第一种：作者给了一个定理，这个定理给出了一种更简单的计算方法，这个计算中只需要计算一次梯度就可以了。这个技巧使得计算级数项的梯度的次数变为 1.

![屏幕快照 2021-08-06 下午6.08.17](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-06 下午6.08.17.png)

第二个技巧：在计算梯度时，利用链式法则，在前传时计算一部分梯度，剩下的在后传的时候计算，这样内存量的计算会进一步减少。
$$
\frac{\partial \mathcal{L}}{\partial \theta}=\underbrace{\frac{\partial \mathcal{L}}{\partial \log \operatorname{det}\left(I+J_{g}(x, \theta)\right)}}_{\text {scalar }} \underbrace{\frac{\partial \log \operatorname{det}\left(I+J_{g}(x, \theta)\right)}{\partial \theta}}_{\text {vector }}
$$
综合两个技巧，可以让内存量变得和一个 residual 网络相同。

### 激活函数的选择

由于这种方法，需要计算二次梯度。作者表示，激活函数的二次导数最好不要出现梯度消失的情况。所以作者给出了两个他们认为合理的条件：

1. 一阶导数有界满足 Lipschitc 条件。
2. 二阶导数在一阶导数接近 1 的时候不会为 0.

 Swish 函数满足条件 2，但是稍微不满足条件1，所以他们修改了 Swish 函数，定义为：
$$
\operatorname{LipSwish}(z):=\operatorname{Swish}(z) / 1.1=z \cdot \sigma(\beta z) / 1.1
$$


## 实验

网络结构参考了 Behrmann，但是没有下采样（所以内存的需求量变大了），详细的网络结构参考附录 E

整体上是用 residual 的结构替换 coupling 的结构（对于 flow 模型而言）

数据集用的是 MNIST 和 CIFAR10

Lipschitz 条件应该是通过谱范数正则项来实现的。

关于生成的质量，我认为不算很突出。作者发现使用 temperature annealing 的策略是无效的，不能使网络生成的结果平滑到分布的 mode 上。

这里的消融实验就是和不使用这篇文章提出的无偏估计以及新的激活函数的比较





## 参考文献

Jens Behrmann, Will Grathwohl, Ricky T. Q. Chen, David Duvenaud, and Jörn-Henrik Jacobsen. Invertible residual networks. International Conference on Machine Learning, 2019.
