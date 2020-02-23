### Paper:

Learning Energy-Based Models in High-dimensional Spaces with Multi-scale Denoising Score Matching

### Author:

Anonymous authors. ICLR 2020 under review

### Year:

2020

### Notes:

总结：作者的做法延续了基于能量函数生成模型的做法。基本框架是要 学习出一个能量函数。学习方式是极小化DSM，作者因为是用了不同的噪声水平，因此是极小化MDSM。能量函数用一个神经网络来表达，是一个标量值。训练时根据MDSM的式子训练，需要用到二阶的导数。当能量函数训练完，用朗之万动态的方法对能量函数进行采样，得到生成的图片。

#### Abstract and Introduction

从摘要的内容看，作者的目标是在高维数据空间中有效地训练 Energy-Based Model。作者分析证明了用不同的噪声水平的去噪 score matching 可以有效训练 EBM，并且提出了新的训练方式 with multi-scale denoising.

EBM 定义：对每一个数据分配一个能量 $E(x)$， 并且由能量直接显示地定义了概率，通过玻尔兹曼分布的形式，即指数族分布形式：$p_m(x) = e^{-E(x)}/Z$. 

Score matching 定义：score function 被定义为对数密度函数的梯度 ，或负能量函数的梯度（两者是一样的）。

如何得到能量函数？借助 SM 的定义，目标是极小化两个定义下的差值2范数。
$$
L_{S M}(\theta)=\mathbb{E}_{p_{\sigma 0}(\tilde{\boldsymbol{x}})}\left\|\nabla_{\tilde{\boldsymbol{x}}} \log \left(p_{\sigma_{0}}(\tilde{\boldsymbol{x}})\right)+\nabla_{\tilde{\boldsymbol{x}}} E(\tilde{\boldsymbol{x}} ; \theta)\right\|^{2}\\
\tilde{\boldsymbol{x}} 是数据，后一项是需要估计的能量函数。需要说明的是，假设数据的分布具有形式：\\
p_{\sigma_{0}}(\tilde{\boldsymbol{x}})=\int q_{\sigma_{0}}(\tilde{\boldsymbol{x}} | \boldsymbol{x}) p(\boldsymbol{x}) d \boldsymbol{x} \text{ （似乎这里的}\tilde{x} 是真实数据加上噪声之后的观测的数据）
$$
Vincent 通过定义 DSM 建立了去噪自编码和SM的关系。他证明了 $L_{DSM}$ 等价于 $L_{SM}$：
$$
L_{D S M}(\theta)=\mathbb{E}_{p_{\sigma_{0}}(\tilde{\boldsymbol{x}}, \boldsymbol{x})}\left\|\nabla_{\tilde{\boldsymbol{x}}} \log \left(q_{\sigma 0}(\tilde{\boldsymbol{x}} | \boldsymbol{x})\right)+\nabla_{\tilde{\boldsymbol{x}}} E(\tilde{\boldsymbol{x}} ; \theta)\right\|^{2}\\
可以看作将\log \left(p_{\sigma_{0}}(\tilde{\boldsymbol{x}})\right) 用 \log \left(q_{\sigma 0}(\tilde{\boldsymbol{x}}| \boldsymbol{x})\right) 替换
$$
这样的好处是后者更容易估计。如果噪声是高斯的，就可以化简为：
$$
L_{D S M}(\theta)=\mathbb{E}_{p_{\sigma 0}(\tilde{\boldsymbol{x}}, \boldsymbol{x})}\left\|\boldsymbol{x}-\tilde{\boldsymbol{x}}+\sigma_{0}^{2} \nabla_{\tilde{\boldsymbol{x}}} E(\tilde{\boldsymbol{x}} ; \theta)\right\|^{2}
$$
有了这些定义的基础，作者接下来的内容就是分析极小化 DSM 时，单个噪声水平时不够的，需要多个噪声水平，并且提出自己的做法。

#### DSM的几何解释

Song and Ermon (2019) 也使用了不同的噪声水平，但作者说他们的工作和本文的工作有一些区别。但是没看懂。

首先作者假设了我们要学习的高维数据分布是位于低维流形上的。那么如何计算流形外面的区域的梯度是最大的问题。作者说明了高斯随机分布的两个性质：1. 很大概率下，模长是 $\sqrt{d}\sigma$，$d$ 是数据维数，$\sigma$ 是高斯分布的标准差啊。2. 大部分随机向量和固定的向量是正交的或者接近正交。在这样的假设下，如果真实数据 $x$ 对应的噪声数据 $\tilde{x}$ 的噪声服从高斯分布，那么就可以看成是过 $\tilde{x}$ 的一条直线和流形 $\mathcal{X}$ 的切面 $T_x\mathcal{X}$ 垂直，并且交于 $x$。由于高斯随机向量的模长大概率是 $\sqrt{d}\sigma$，因此 $\tilde{x}$ 基本上落在 $x$ 的距离为 $(\sqrt{d} \sigma-\epsilon, \sqrt{d} \sigma+\epsilon)$ 的空间中，$\epsilon << \sqrt{d}\sigma$。如果只使用一个噪声水平，那么就有很多空间里的点没有出现在训练中，因此效果不好。

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-16%E4%B8%8A%E5%8D%8811.03.28.png)

另外，作者表明，当噪声越大，学到的特征往往具有更大的尺度，或者说更平滑。

#### 作者提出的方法

既然单一的噪声水平不好，那就用一个分布来生成。原来的目标函数就变成：
$$
L_{M S M}(\theta)=\mathbb{E}_{p_{M}(\tilde{\boldsymbol{x}})}\left\|\nabla_{\tilde{\boldsymbol{x}}} \log \left(p_{\sigma_{0}}(\tilde{\boldsymbol{x}})\right)+\nabla_{\tilde{\boldsymbol{x}}} E(\tilde{\boldsymbol{x}} ; \theta)\right\|^{2}\\
L_{M D S M^{*}}=\mathbb{E}_{p_{M}(\tilde{\boldsymbol{x}}) q_{\sigma_{0}}(\boldsymbol{x} | \tilde{\boldsymbol{x}})}\left\|\nabla_{\tilde{\boldsymbol{x}}} \log \left(q_{\sigma 0}(\tilde{\boldsymbol{x}} | \boldsymbol{x})\right)+\nabla_{\tilde{\boldsymbol{x}}} E(\tilde{\boldsymbol{x}} ; \theta)\right\|^{2}
$$
需要说明的是，作者始终是用了 $q_{\sigma_0}$，并且说这个 $\sigma_0$ 和选择的噪声水平不一样。不太理解。

不过最后作者选择了多个固定值。
$$
L(\theta)=\sum_{\sigma \in\left\{\sigma_{1} \cdots \sigma_{K}\right\}} \mathbb{E}_{q_{\sigma}(\tilde{\boldsymbol{x}} | \boldsymbol{x}) p(\boldsymbol{x})} l(\sigma)\left\|\boldsymbol{x}-\tilde{\boldsymbol{x}}+\sigma_{0}^{2} \nabla_{\tilde{\boldsymbol{x}}} E(\tilde{\boldsymbol{x}} ; \theta)\right\|^{2}
$$
训练的时候作者应该是用去噪自编码的形式。但是从学到的能量函数采样的时候，还是用了 Langevin dynamics，不过增加了一些技巧 3.2 section.
$$
\boldsymbol{x}_{t+1}=\boldsymbol{x}_{t}-\frac{\epsilon^{2}}{2} \nabla_{\boldsymbol{x}} E\left(\boldsymbol{x}_{t} ; \theta\right)+\epsilon \sqrt{T_{t}} \mathcal{N}\left(0, I_{d}\right)
$$
作者使用的网络是ResNet。按照 (5) 式，训练的过程应该是网络的输入是 $\tilde{x}$，即噪声图片，网络最后的输出是标量，表示能量函数值，计算 $\tilde{x}$ 的梯度，根据 (5) 式得到损失函数值，计算梯度（也就是说需要计算网络参数的二阶的导数 $\dfrac{\part^2 E(\tilde{x}; \theta)}{\part{\tilde{x}}\part{\theta}}$，由这个梯度来更新网络参数。不过不知道这个怎么实现的。

另外，作者在附录中对SM，DSM，MDSM有一些推导，可以看。

### Bibtex:

