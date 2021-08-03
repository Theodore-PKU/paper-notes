# Maximum Likelihood Training of Score-Based Diffusion Models

[TOC]

## 信息

Author: Yang Song, Conor Durkan, Iain Murray, Stefano Ermon

Year: arXiv 2021

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/maximum-likelihood-training-of-score-based-diffusion-models.pdf)

Bibtex:

```latex
@article{song2021maximum,
  title={Maximum Likelihood Training of Score-Based Diffusion Models},
  author={Song, Yang and Durkan, Conor and Murray, Iain and Ermon, Stefano},
  journal={arXiv e-prints},
  pages={arXiv--2101},
  year={2021}
}
```



## 概括

score-based 生成模型和扩散概率模型，本质是一样的，都是 score-based 扩散模型。这种模型和连续 normalizing flow 模型之间存在联系。然而，训练的方式是不同的。CNF 用的是极大似然函数，但是 score-based 模型则是用 weighted score matching loss。虽然后者更有效率，但是并不能保证这样训练出来的模型的概率估计更准确。

这篇文章提出的方法是通过对原来的 weighted score matching function 的权重进行特别的设计，并证明这样的损失函数是负对数似然概率的上界，而且当 score 模型更接近真实的 reverse-time SDE 时，这个上界更紧。

作者还设计了一些额外的技巧来减少训练时的 variance？这个还没理解是什么意思。

作者还说，这个 loss 和 ELBO 之间有很大的联系。这个也要关注一下。

作者的实验是对不同的数据集，使用不同的模型、网络结构，通过更换损失函数来说明这个新的训练损失函数可以有效提高概率估计的准确度。这里的疑问是，模型仍然是 score-based 扩散模型吗，只是在计算概率的时候考虑了对应的 CNF 模型而已？本质上的训练和 CNF 或者说 flow 模型的训练不同？这个需要后续论文阅读的验证。



## 方法

### SDE 生成模型

这里不过多描述 SDE 了。只简单介绍一下。

首先作者把 SDE 模型分成了三类（按照 ICLR 2021 的那篇文章），Variance Exploding (VE) SDEs, Variance Preserving (VP) SDEs, and subVP SDEs.

整个模型的核心是估计 score function。通过下面的损失函数：
$$
\mathcal{J}_{\text {SM }}(\boldsymbol{\theta} ; \lambda(\cdot)):=\frac{1}{2} \int_{0}^{T} \mathbb{E}_{p_{t}(\mathbf{x})}\left[\lambda(t)\left\|\nabla_{\mathbf{x}} \log p_{t}(\mathbf{x})-\boldsymbol{s}_{\boldsymbol{\theta}}(\mathbf{x}, t)\right\|_{2}^{2}\right] \mathrm{d} t
$$
作者称之为 weighted combination of score matching losses。我们简单称之为带权重的 score loss。

### SDE 生成模型导出的概率模型

这里作者给出了两个概率模型。第一个概率模型其实就是 SDE 模型本身。利用逆 diffusion 过程，我们可以 sample 出原始信号。第二个就是对应的 ODE 模型。ODE 模型可以算精确的概率，但是如果要用这种方式计算似然概率作为损失函数，则计算量太大了。这两个模型在论文中似乎被当作是不同的模型了，我猜测原因是求解过程并不能保证两者完全相同吧。理论上两者应该是相同的。

不过作者表示，虽然 SDE 模型的似然概率算不出来，我们可以提供一个下界，这个要怎么做呢？

### 特殊的 weight

$$
\lambda(t)=g(t)^{2}
$$

这个是作者给出的 weight 选择。

定理1 说明了使用这个 weight，带权重的 score loss 和 KL 散度度量之间的关系。

在这里 KL 散度的度量和极大似然函数是等价的（作者在论文里这么说明，但是没有给更详细的说明了，我们默认这是对的）。

![屏幕快照 2021-08-02 下午8.25.01](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-02 下午8.25.01.png)

这个新的 weight 和原来的 weight 不同。原来的 weight 是为了平衡不同的 score matching losses，并且取得实验上比较好的效果。但是新的 weight 是为了保证和极大似然估计的关系，来自理论的分析。

![屏幕快照 2021-08-02 下午8.28.54](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-02 下午8.28.54.png)

这个图给出了作者定义的三类 score-based 扩散模型对应的 weight 比较。

### 其他定理

接下来作者回答了两个很重要的问题。

1. 什么情况下，新的带权重的 score loss 和 KL 散度 loss 的误差是 0
2. 什么情况下，ODE 和 SDE 的两个分布之间有什么样的关系。

定理 2 给出了这两个问题的答案。

![屏幕快照 2021-08-02 下午8.33.02](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-02 下午8.33.02.png)

也就是说，只要 score function 估计是完全准确的，那么上述两个问题的答案就是：误差为 0，ODE 和 SDE 的两个分布等价。

但是在实际中，估计的 score model 并不是完全匹配 score function。但作者的实验说明，这个 loss 的训练，确实有效地改善了 oDE 计算的似然概率的结果。

定理3 则给出了对 SDE 分布的单个点的似然概率的上界公式。这个定理的条件和定理1完全相同。

![屏幕快照 2021-08-02 下午8.37.44](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-02 下午8.37.44.png)

但是这个公式似乎特别复杂。需要用到 skilling-hutchinson 技巧[39,21] 才能算。

### 训练的其他技巧

作者观察到，使用新的损失函数训练时，会带来 variance，作者提出用 importance sampling 来减少这种训练过程的变动。

另外，借助 variantional dequantization 策略（flow++ 的 dequantization 方法），来减少模型训练时，连续和离散之间的差异。作者把这两个技巧结合起来，实验中取得了很好的效果。其中 ODE 分布模型称为 ScoreFlow，效果超过了 SOTA 的自回归模型，数据集用的是 CIFAR-10 和 ImageNet32

下面先介绍 importance sampling

原本训练时，采用的是 MC 的方法，对 t 做随机采样，然后再计算 t 时刻的损失函数值，作为整个时间积分的估计。很显然，这么做会增加损失函数的 variance。

作者提出的方法，使用 $p(t) = g(t)^2/(\alpha(t)^2Z)$ 的分布对 t 进行采样。这样会使得新的损失函数变成原来的损失函数。$\alpha$ 是原来的 weight。Z 是归一化常数。

但是这样做，这还是对 t 对积分的估计吗？有更高的概率 sample 到权重更大的 t，这和原始的方法有什么区别吗？作者没有回答这个问题，但是我觉得是有问题的。实验结果是对 VP 有较大的改进，对于 subVP 改进比较小。不管怎么说，这两种采样方法，是有区别的，至于理论上的区别和实践的差异，就不好解释了。

### Variational dequantization

作者用 flow++ 的模型作为 variational dequantization 的 u 的模型。从后文的描述来看，似乎作者先训练了扩散模型，然后再训练了 flow++ 模型。如果不是这样，flow++ 模型是无法训练的。

这里用到的损失函数是：
$$
\max _{\boldsymbol{\phi}} \mathbb{E}_{\mathbf{x} \sim p(\mathbf{x})} \mathbb{E}_{\mathbf{u} \sim q_{\phi}(\cdot \mid \mathbf{x})}\left[-\mathcal{L}_{\boldsymbol{\theta}}^{\mathrm{DSM}}(\mathbf{x}+\mathbf{u})-\log q_{\boldsymbol{\phi}}(\mathbf{u} \mid \mathbf{x})\right]
$$
我个人不太理解这样做的意义在哪里，和 uniform dequantization 的差异对训练扩散模型有影响吗？我觉得这个非常奇怪，如果仅仅是在计算NLL 之类的时候加入了这个 dequantization，当然两者会差异。但是如果训练都一样。

作者实际上的做法应该是训练 variational dequantization 的时候，扩散模型是固定的，但是不知道这两个具体是如何结合起来训练的，论文可能没有说清楚。



## 实验

实验的内容直接看表就可以了。

![屏幕快照 2021-08-02 下午8.57.20](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-02 下午8.57.20.png)

