# Masked Autoregressive Flow for Density Estimation

[TOC]

### 信息

Authors: George Papamakarios, Theo Pavlakou and Iain Murray

University of Edinburgh

NeurIPS 2017

```latex
@inproceedings{papamakarios2017masked,
  title={Masked autoregressive flow for density estimation},
  author={Papamakarios, George and Pavlakou, Theo and Murray, Iain},
  booktitle={Advances in Neural Information Processing Systems},
  pages={2338--2347},
  year={2017}
}
```

引用 300+

[Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/masked-autoregressive-flow-for-density-estimation.pdf)



### 概括

这篇文章提出了 Masked Autoregressive Flow (MAF) 模型，这是一种 normalizing flow 模型，其中的基本 block 是 Masked Autoencoder for Distribution Estimation (MADE) 模型。MADE 模型是一个自回归模型（全连接网络）。因此 MAF 相当于将自回归模型和 normalizing flow 模型结合起来。这种做法和 Inverse Autogressive Flow 模型是非常类似的。事实上，自回归模型在高斯分布作为条件分布的情形下，利用重参数原理可以推导出隐变量和原始变量之间的变换关系。这个关系恰好和 flow-based 模型一样。后者 (IAF) 模型也利用了 MADE 模型，但是两者有一些差别。不过它们都可以看作是 Real NVP 模型的一种推广。

实验部分，这篇论文既没有给出图片，叙述地也相对简单。

这篇文章主要的贡献在于提出 MAF 模型。这个模型和 IAF 等模型相比，在计算 x 的概率时很高效，但是在进行 variance inference 时比较低效。IAF 模型则相反。事实上，这两个模型可以看成是一个 flow reverse 的关系。关于概率计算和变分推断的讨论是值得注意的。这也足够引发一些思考。

当然，这篇文章将自回归模型当作是 flow 模型中的一个变换，前提是隐变量是高斯分布。但是实际中 flow 模型的隐变量不一定是高斯分布（中间层的隐变量），只有最后一个输出的隐变量和 split out 的隐变量才是（传递下去的隐变量都没有参与损失函数的计算，无法判定是什么分布），这是否意味着反过来推导出来的自回归模型的分布不再是高斯模型。这个问题也值得思考。我感觉这一类模型有一个核心问题，就是隐变量分布是怎么保证是高斯的，仅仅用似然函数极大化总觉得不够可靠。

### 方法

#### 自回归模型和 flow 模型

自回归模型的本质是将信号 $\mathbf{x}$ 看成一个序列，任何一个分量（除了第一个）都可以看成是此前分量的一个条件分布。而 $\mathbf{x}$ 的概率模型就用这一系列的条件概率的乘积获得。一般来说，这个条件分布都采用高斯分布的形式。因此有：
$$
p\left(x_{i} \mid \mathbf{x}_{1: i-1}\right)=\mathcal{N}\left(x_{i} \mid \mu_{i},\left(\exp \alpha_{i}\right)^{2}\right) \quad \text { where } \quad \mu_{i}=f_{\mu_{i}}\left(\mathbf{x}_{1: i-1}\right) \text { and } \alpha_{i}=f_{\alpha_{i}}\left(\mathbf{x}_{1: i-1}\right)
$$
这个公式看似和 flow-based 模型没有关系，但是如果使用重参数的方法，就可以从中得到一个隐变量的模型。

利用 $x_{i}=u_{i} \exp \alpha_{i}+\mu_{i}, u_i \sim \mathcal{N}(0, 1)$，可以得到向量 $\mathbf{u} = (u_1, \cdots, u_I)$，而且这个向量的每个分量都是一个随机数。因此自回归模型也可以看成是从 $\mathbf{u}$ 计算出 $\mathbf{x}$，对于第一个分量是特例。假设有了完整的 $\mathbf{u}$，就可以通过函数 $f$ 获得 $\mathbf{x}$，其中
$$
x_i= u_i \exp f_{\alpha_i}(\mathbf{x}_{1:i-1}) + f_{\mu_i}(\mathbf{x}_{1:i-1}) = u_i \exp f_{\alpha_i}(f_{1:i-1}(\mathbf{u}_{1:i-1})) + f_{\mu_i}(f_{1:i-1}(\mathbf{u}_{1:i-1}))
$$
类似的，通过 $u_{i}=\left(x_{i}-\mu_{i}\right) \exp \left(-\alpha_{i}\right)$ 这个式子，也很容易得到逆运算的形式。

这种自回归的形式，在高斯条件分布和重参数的两个假设、条件下就变成了一个 flow 形式的模型。而且，因为 $\alpha_i$ 的计算仅仅涉及到 1:i-1 的分量，所以 Jacobian 矩阵也满足三角矩阵的形式，行列式很好计算。因此这也满足了 flow-based 模型的条件。
$$
p(\mathbf{x})=\pi_{u}\left(f^{-1}(\mathbf{x})\right)\left|\operatorname{det}\left(\frac{\partial f^{-1}}{\partial \mathbf{x}}\right)\right|
$$

$$
\left|\operatorname{det}\left(\frac{\partial f^{-1}}{\partial \mathbf{x}}\right)\right|=\exp \left(-\sum_{i} \alpha_{i}\right) \quad \text { where } \quad \alpha_{i}=f_{\alpha_{i}}\left(\mathbf{x}_{1: i-1}\right)
$$

上述的推导首先是在 IAF 模型中提出来的。

有个这个自回归模型到 flow 模型的变化之后，就可以用一系列的自回归模型构成一个 normalizing flow model。

#### MAF 和 IAF 的关系

这个部分是这篇文章里值得思考的一个部分。这两个模型都利用 MADE 模型作为 block（计算 $\alpha, \mu$），而且也构成了 normalizing flow 的形式。但是在计算 $\alpha, \mu$ 的时候，两者的参数不同。

在 MAF 模型中，使用的是 $\mu_{i}=f_{\mu_{i}}\left(\mathbf{x}_{1: i-1}\right) \text { and } \alpha_{i}=f_{\alpha_{i}}\left(\mathbf{x}_{1: i-1}\right)$，虽然 $\mathbf{x}_{1:i-1}$ 也可以用 $\mathbf{u}$ 来表示。

在 IAF 模型中，则是 $\mu_{i}=f_{\mu_{i}}\left(\mathbf{u}_{1: i-1}\right) \text { and } \alpha_{i}=f_{\alpha_{i}}\left(\mathbf{u}_{1: i-1}\right)$. 

倘若在 MAF 模型中，进行逆运算的时候，把 $\mu_i, \alpha_i$ 的符号改成正号，那么就变成 IAF 模型了。

这两个模型的差别在于，MAF 模型计算 $\mathbf{x}$ 的概率时只需要 one-pass，而计算 variance inference 时需要 D-passes，D 表示输入的维度。这是因为计算概率时只需要计算：
$$
p_{x}(\mathbf{x})=\pi_{u}\left(f^{-1}(\mathbf{x})\right)\left|\operatorname{det}\left(\frac{\partial f^{-1}}{\partial \mathbf{x}}\right)\right|
$$
很显然，从 $\mathbf{x}$ 到 $\mathbf{u}$，$f$ 的计算时直接的。但是 IAF 模型，因为 $\mu, \alpha$ 的计算用的是 $\mathbf{u}$，所以需要先算出 $\mathbf{u}_{1:i-1}$，才能算出 $\mathbf{u}_i$。variance inference 恰好相反。$\mathbf{u}$ 在 sampling 之后，IAF 模型只需要 one-pass 就可以算出 $\mathbf{x}$，而 MAF 模型则需要 D-pass。

因此作者说这两个模型的侧重点是不同的。

（这个差异似乎反映出 IAF 模型在训练的时候比较麻烦？因为 $p_x(\mathbf{x})$ 并不好算，一般的 flow-based 模型反而没有这个问题，因为没有采用自回归的形式，他们的 $\alpha, \mu$ 的参数还是以 $\mathbf{x}$ 为主。不过他们因为采用了 affine coupling 的形式，所以 $\mathbf{x}$ 和 $\mathbf{u}$ 没有区别了。

#### MAF 和 Real NVP 的关系

这里探讨的其实是和 affine coupling layer 的关系。一般来说 affine coupling layer 的形式是：
$$
\begin{aligned}
\mathbf{x}_{1: d} &=\mathbf{u}_{1: d} & & \boldsymbol{\mu}=f_{\mu}\left(\mathbf{u}_{1: d}\right) \\
\mathbf{x}_{d+1: D} &=\mathbf{u}_{d+1: D} \odot \exp \boldsymbol{\alpha}+\boldsymbol{\mu} & \text { where } & \boldsymbol{\alpha}=f_{\alpha}\left(\mathbf{u}_{1: d}\right)
\end{aligned}
$$
由于 $\alpha, \mu$ 都是用 $\mathbf{x}_{1:d}$ 或者 $\mathbf{u}_{1:d}$ 得到。所以当 $i \leq d$ 时，相当于 IAF 和 MAF 中 $\mu_i = \alpha_i = 0$，当 $i > d$ 时，相当于，只 conditional on $i:d$ 的部分。但是这样的好处是不论是推断还是计算概率，都只要 one-pass。

#### Condtional MAF

作者也考虑了 condtional 的情况，其实就是在自回归的条件分布里增加了一个额外的条件。实际当作，这个条件是作为 MADE 网络的额外输入。

#### 基本 block MADE

MADE 模型在论文中，似乎就是一个全连接网络。这个网络要考虑的就是如何限制最终输出的第 i 个分量，在计算上只和 1:i-1 的输入分量有关。我的理解是每个节点有一个 degree 的标注，计算的时候会根据这个标注来实现 masked weight matric 的计算。也就是参与计算的部分必须是比当前 degree 小的那些输入节点。对于隐藏层，也需要设置 degree，如果隐藏层的节点数量比输入输出少，那么会随机地选择部分 degree。如果多，只要涵盖所有的 degree 就可以了。



### 实验

作者实验了三种模型：MADE（包括混合高斯分布作为条件分布的模型）、Real NVP（但是和原论文略有区别）、MAF。

coupling layer 之后，作者加了一个 BN 层。

