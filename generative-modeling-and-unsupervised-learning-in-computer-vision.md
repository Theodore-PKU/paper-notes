### Paper:

Generative Modeling and Unsupervised Learning in Computer Vision

### Author:

Jianwen Xie

### Year:

2016

### Notes:

这是作者攻读博士学位时写的一篇长文，相当于自己的工作的总体总结。包含了数篇论文的内容。

#### Chapter 1 Introduction

##### 1.1 Motivation and objective

这篇文章的目的是要发展一种用于表示自然图像的视觉模式的生成模型。

生成模型有助于学习到有用的特征和表示，对于其他视觉任务是有帮助的。但是生成模型没有label，所以是无监督的学习。学习到的特征和表示比判别模型更有解释性，特别是当学到的模型是稀疏的。

> 思考：所以从训练的角度来说，判别模型是通过label来限制和刻画图像的结构，生成模型是直接从数据来找到其中的结构。所以两者一定是可以结合的。也许GAN是某一种方式，但不一定是最好的方式。

##### 1.2 Proposed models and algorithms

这部分是关于作者提出的一些模型的简单描述。他们具有时间上的连续性，不完全相同，但是非常相关。

这部分应该回头再读。

**Sparse FRAME model**

**Generative boosting**

**Deformable-part sparse FRAME model**

**Unsupervised codebook learning**

**Generative ConvNet as hierarchical FRAME**

 **Spatial-temporal Generative ConvNet for dynamic textures**

**Hopﬁeld auto-encoder**

#### Chapter 2 Learning Sparse FRAME Models for Natural Image Patterns

##### 2.1 Introduction

**Background and motivation**

关于字典学习的一些介绍。

> 思考：想到一个问题，图像虽然在小波变换下是稀疏的，但是这个稀疏应该没有将图像的结构表示出来。如果我们随机生成一个稀疏的小波系数，重建回来的图像是真实的吗？如果不是，就说明小波域下的稀疏性只是图像的一个属性的刻画，并不是完全的刻画。由此想到GAN，输入是一个随机分布，它可以把这个随机分布映射到图像分布，是否意味着建立了某一种刻画？反过来，对任何一个图像，有一个对应的随机分布中的点。但是这种要求随机分布中的所有点都映射到合理的真实图像域的做法，缺乏统计上的性质。

作者提出的问题是：小波这一层的表示之上是什么样的表示。作者的想法是通过一类统计模型来表示，每个模型选择字典里的一部分小波，对选择的小波系数施加一个概率分布。

困难：对系数的建模是困难的。所以作者考虑的是图像对选择的小波的响应的建模。选择的数学模型是 FRAME (Filters, Random ﬁeld, And Maximum Entropy) model of Zhu, Wu, and Mumford (1997) [120]

基于 FRAME 模型，提出 sparse FRAME 模型，并且提出了两阶段的学习方式。作者指出，这篇研究是不考虑filters的可选范围，默认已经存在一个给定的小波字典。如果有训练数据，可以用数据训练出来。只是这个过程不在讨论的范围内。

**Related work**

Markov random ﬁeld models，也被称为 energy-based models，exponential family models，Gibbs distributions。（都是出现过的名词，原来都是一个东西）

马尔可夫随机场模型用一个能量函数定义，可能包含潜在变量或隐藏单元。这方面的工作主要在于估计训练图像的概率分布，而不是重建图像。

Sparse coding models，这一类模型关注的是重建图像，而不是估计信号的概率分布。

作者提出的模型可以拓展到学习一个层次模型。

作者提出的模型可以看作是原始  FRAME 模型和 active basis model (不懂) 的结合。

##### 2.2 Inhomogeneous FRAME model

**Model and learning algorithm**

记号：图像训练集 $\left\{\mathbf{I}_{m}, m=1, \ldots, M\right\}$, Gabor 小波 $B_{x, s, \alpha}$（短时傅立叶变换），$s$ 表示 scale,  $\alpha$ 表示方向。假定 $B_{x, s, \alpha}$ 标准化为单位模长。

模型：
$$
p(\mathbf{I} ; \lambda)=\frac{1}{Z(\lambda)} \exp \left(\sum_{x, s, \alpha} \lambda_{x, s, \alpha}\left(\left\langle\mathbf{I}, B_{x, s, \alpha}\right\rangle\right)\right) q(\mathbf{I})\\
\begin{aligned} Z(\lambda) &=\int \exp \left(\sum_{x, s, \alpha} \lambda_{x, s, \alpha}\left(\left\langle\mathbf{I}, B_{x, s, \alpha}\right\rangle\right)\right) q(\mathbf{I}) d \mathbf{I} \\ &=\mathrm{E}_{q}\left[\exp \left(\sum_{x, s, \alpha} \lambda_{x, s, \alpha}\left(\left\langle\mathbf{I}, B_{x, s, \alpha}\right\rangle\right)\right)\right] \end{aligned}
$$
在原始的 FRAME 模型中，$\lambda_{x,s,\alpha}$ 和 $x$ 不想关，和 $s, \alpha$ 相关。这样的设定和纹理相关的。但是对于物体模式的建模，应该是相关的。原始的各向同性 FRAME 模型中，势能函数 $\lambda_{x,s,\alpha}$ 是非参的，但是由于较小的数据集，作者使用参数化的势能函数 $\lambda_{x,s,\alpha}() = \lambda_{x,s,\alpha}|r|$ 

在很多马尔可夫随机场模型中，包括原始的 FRAME 模型，$q(\mathbf{I})$ 是均匀分布，作者这里假设为高斯白噪声的形式：
$$
q(\mathbf{I})=\frac{1}{\left(2 \pi \sigma^{2}\right)^{|\mathcal{D}| / 2}} \exp \left(-\frac{1}{2 \sigma^{2}} \sum_{x} \mathbf{I}(x)^{2}\right)
$$
这样，$p(\mathbf{I};\lambda)$ 就是一个 exponential family model 的特例。可以用极大化似然概率估计。对数似然函数为：
$$
\begin{aligned} L(\lambda)=& \frac{1}{M} \sum_{m=1}^{M} \log p\left(\mathbf{I}_{m} ; \lambda\right) \\=& \frac{1}{M} \sum_{m=1}^{M} \sum_{x, s, \alpha} \lambda_{x, s, \alpha}\left|\left\langle\mathbf{I}_{m}, B_{x, s, \alpha}\right\rangle\right|-\log Z(\lambda) \\ &+\frac{1}{M} \sum_{m=1}^{M} \log q\left(\mathbf{I}_{m}\right) \end{aligned}
$$
使用梯度方法求解：
$$
\begin{aligned} & \frac{\partial L(\lambda)}{\partial \lambda_{x, s, \alpha}} \\=& \frac{1}{M} \sum_{m=1}^{M}\left|\left\langle\mathbf{I}_{m}, B_{x, s, \alpha}\right\rangle\right|-\frac{1}{Z(\lambda)} \frac{\partial Z(\lambda)}{\partial \lambda_{x, s, \alpha}} \\=& \frac{1}{M} \sum_{m=1}^{M}\left|\left\langle\mathbf{I}_{m}, B_{x, s, \alpha}\right\rangle\right|-\int \frac{1}{Z(\lambda)} \exp \left(\sum_{x, s, \alpha} \lambda_{x, s, \alpha}\left|\left\langle\mathbf{I}, B_{x, s, \alpha}\right\rangle\right|\right) q(\mathbf{I})\left|\left\langle\mathbf{I}, B_{x, s, \alpha}\right\rangle\right| d \mathbf{I} \\=& \frac{1}{M} \sum_{m=1}^{M}\left|\left\langle\mathbf{I}_{m}, B_{x, s, \alpha}\right\rangle\right|-\int p(\mathbf{I} ; \lambda)\left|\left\langle\mathbf{I}, B_{x, s, \alpha}\right\rangle\right| d \mathbf{I} \\=& \frac{1}{M} \sum_{m=1}^{M}\left|\left\langle\mathbf{I}_{m}, B_{x, s, \alpha}\right\rangle\right|-\mathrm{E}_{p(\mathbf{I} ; \lambda)}\left[\left|\left\langle\mathbf{I}, B_{x, s, \alpha}\right\rangle\right|\right], \forall x, s, \alpha \end{aligned}
$$
更新算法为：
$$
\lambda_{x, s, \alpha}^{(t+1)}=\lambda_{x, s, \alpha}^{(t)}+\gamma_{t}\left(\frac{1}{M} \sum_{m=1}^{M}\left|\left\langle\mathbf{I}_{m}, B_{x, s, \alpha}\right\rangle\right|-\mathrm{E}_{p\left(\mathbf{I} ; \lambda^{(t)}\right)}\left[\left|\left\langle\mathbf{I}, B_{x, s, \alpha}\right\rangle\right|\right]\right)
$$
为了估计第二项，通过一个生成图像的采样集$\left\{\tilde{\mathbf{I}}_{m}, m=1, \ldots, \tilde{M}\right\} \text { generated from } p\left(\mathbf{I} ; \lambda^{(t)}\right)$来估计:
$$
\mathrm{E}_{p(\mathrm{I} ; \lambda)}\left[\left\langle\mathbf{I}, B_{x, s, \alpha}\right\rangle |\right] \approx \frac{1}{\tilde{M}} \sum_{m=1}^{\tilde{M}}\left|\left\langle\tilde{\mathbf{I}}_{m}, B_{x, s, \alpha}\right\rangle\right|
$$
生成图像的采样方式为 Hamiltonian Monte Carlo (HMC)，作者说这个过程包含自下而上的卷积运算和自上而下的卷积运算（论文2.10.1节，需要一个动能函数和拒绝概率）。利用 HMC 和 warm start，$\{\tilde{\mathbf{I}}_{m}\}$ 是用多个 paralle chains 生成。

对于标准化常数的估计也可以用类似的方法。
$$
\begin{aligned} & \frac{Z\left(\lambda^{(t+1)}\right)}{Z\left(\lambda^{(t)}\right)} \\=& \int \frac{1}{Z\left(\lambda^{(t)}\right)} \exp \left(\sum_{x, s, \alpha} \lambda_{x, s, \alpha}^{(t)}\left|\left\langle\mathbf{I}, B_{x, s, \alpha}\right\rangle\right|\right) q(\mathbf{I}) \times \frac{\exp \left(\sum_{x, s, \alpha} \lambda_{x, s, \alpha}^{(t+1)}\left|\left\langle\mathbf{I}, B_{x, s, \alpha}\right\rangle\right|\right)}{\exp \left(\sum_{x, s, \alpha} \lambda_{x, s, \alpha}^{(t)}\left|\left\langle\mathbf{I}, B_{x, s, \alpha}\right\rangle\right|\right)} d \mathbf{I} \\=& \int p\left(\mathbf{I} ; \lambda^{(t)}\right) \exp \left(\sum_{x, s, \alpha}\left(\lambda_{x, s, \alpha}^{(t+1)}-\lambda_{x, s, \alpha}^{(t)}\right) \times\left|\left\langle\mathbf{I}, B_{x, s, \alpha}\right\rangle\right|\right) d \mathbf{I} \\=& \mathrm{E}_{p\left(\mathbf{I} ; \lambda^{(t)}\right)}\left[\exp \left(\sum_{x, s, \alpha}\left(\lambda_{x, s, \alpha}^{(t+1)}-\lambda_{x, s, \alpha}^{(t)}\right) \times\left|\left\langle\mathbf{I}, B_{x, s, \alpha}\right\rangle\right|\right)\right] \end{aligned}\\
\frac{Z\left(\lambda^{(t+1)}\right)}{Z\left(\lambda^{(t)}\right)} \approx \frac{1}{\tilde{M}} \sum_{m=1}^{\tilde{M}}\left[\exp \left(\sum_{x, s, \alpha}\left(\lambda_{x, s, \alpha}^{(t+1)}-\lambda_{x, s, \alpha}^{(t)}\right) \times\left|\left[\tilde{\mathbf{I}}_{m}, B_{x, s, \alpha}\right\rangle\right|\right)\right]\\
\log Z\left(\lambda^{(t+1)}\right)=\log Z\left(\lambda^{(t)}\right)+\log \frac{Z\left(\lambda^{(t+1)}\right)}{Z\left(\lambda^{(t)}\right)}
$$
随着 t 变化的生成图像![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-12%E4%B8%8B%E5%8D%8812.14.28.png)

算法总结

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-12%E4%B8%8B%E5%8D%8812.19.40.png" style="zoom: 50%;" />

##### 2.3 Sparse FRAME model

2.2节是 dense FRAME，因为每一个基函数都有一个系数 $\lambda_{x,s,\alpha}$，稀疏性体现在只选择一部分的基函数。

**Shared sparse coding**

作者利用 Shared sparse coding 和 sparse FRAME 的关系来选择一组基函数。忽略 deformation，可以把任何一个图像投影到由选中的这组基函数构成的子空间中。
$$
\mathbf{I}_{m}=\sum_{i=1}^{n} c_{m, i} B_{x_{i}, s_{i}, \alpha_{i}}+\epsilon_{m},\mathbf{B}=\left(B_{x_{i}, s_{i}, \alpha_{i}}, i=1, \ldots, n\right)
$$
其中，$c_{m,i}$ 是最小二乘重建误差的系数，$\epsilon_m$ 是垂直于投影空间的残差图像。对于训练集的数据，这一小族基函数 $\mathbf{B}$ 是一样的。

作者给出的求解算法是一个两阶段算法。第一步，选择 $\mathbf{B}$ 使得最小二乘误差项最小；第二步，对给定的 $\mathbf{B}$ 估计 $\lambda$.

考虑了 deformation 的意思是说不完全要求每幅图像都是完全相同的一组基，而是可以在这组基上有细微的变动，这种变动是局限在某个范围内，换句话说，变动以后的基函数和原来的基函数非常接近。
$$
\mathbf{I}_{m}=\sum_{i=1}^{n} c_{m, i} B_{x_{i}+\Delta x_{m, i}, s_{i}, \alpha_{i}+\Delta \alpha_{m, i}}+\epsilon_{m}
$$

> 思考：这种变动是空间上的平移、旋转、放缩。这些考虑是非常符合图像的几何直观。

**The two-stage learning algorithm**

具体算法。

stage 1: 选择 $\mathbf{B}$ 极小化下式
$$
\sum_{m=1}^{M}\left\|\mathbf{I}_{m}-\sum_{i=1}^{n} c_{m, i} B_{x_{i}+\Delta x_{m, i} s_{i}, \alpha_{i}+\Delta \alpha_{m, i}}\right\|^{2}
$$
<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-12%E4%B8%8B%E5%8D%882.32.45.png" style="zoom:50%;" />

stage 1 相当于是找到一个逼近, $\mathbf{B}=\left(B_{x_{i}, s_{i}, \alpha_{i}}, i=1,...,m\right)$ 称为 superposition。用一种贪心的办法找到最大投影，使误差尽量小。因为考虑了 deformation，所以，训练集中的每个图像对应的基函数有细微的差别。

stage 2: 估计 $\lambda$. 对数似然函数为
$$
L\left(\mathbf{I}_{m} | \mathbf{B}, \lambda\right)=\sum_{i=1}^{n} \lambda_{i} \max _{\Delta x, \Delta \alpha}\left|\left\langle\mathbf{I}_{m}, B_{x_{i}+\Delta x, s_{i}, \alpha_{i}+\Delta \alpha}\right\rangle\right|-\log Z(\lambda)
$$
$\lambda$ 的更新方式和 dense FRAME 是一样的。区别在于 deformation：
$$
\lambda_{i}^{(t+1)}=\lambda_{i}^{(t)}+\gamma_{t} \left(\frac{1}{M} \sum_{m=1}^{M} \max _{\Delta x, \Delta \alpha}\left|\left\langle\mathbf{I}_{m}, B_{x_{i}+\Delta x, s_{i}, \alpha_{i}+\Delta \alpha}\right\rangle\right|\right. \left.-\frac{1}{\tilde{M}} \sum_{m=1}^{\tilde{M}}\left|\left\langle\tilde{\mathbf{I}}_{m}, B_{x_{i}, s_{i}, \alpha_{i}}\right\rangle\right|\right) 
$$
对 $\tilde{\mathbf{I}}_m$ 的抽样用的方法也是 HMC。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-12%E4%B8%8B%E5%8D%883.07.31.png" style="zoom:50%;" />

> 思考：不论是 dense FRAME 还是 sparse FRAME，总结起来，都可以看成是某种逼近，但是因为有似然函数，所以是在概率分布意义下的逼近。抽样方式在这种统计的角度下看不符合视觉上的直观。
>
> 想到的观点是这种分布最好不要建立在图像域上，建立在图像域上的分布总是 比较奇怪。

##### 2.4 Detection

检测物体。就是将测试图片带入似然函数中，找到最大的似然函数值对应的区域。对于每一个区域，可以如下计算：
$$
L\left(\mathbf{I} | \mathbf{B}_{X}, \lambda\right)=\sum_{i=1}^{n} \lambda_{i} \max _{\Delta x, \Delta \alpha}\left|\left\langle\mathbf{I}, B_{X+x_{i}+\Delta x, s_{i}, \alpha_{i}+\Delta \alpha}\right\rangle\right|-\log Z(\lambda)
$$
需要考虑的有边界条件、尺度大小（通过不同尺度放缩解决）、旋转、对称变换等。可以对基函数做相应的几何变换。最后找到使得似然函数最大的尺度、旋转角度以及区域。

##### 2.5 Learning with automatic alignment

自动调整区域 $X$，尺度 $S$，角度 $A$. 将检测算法中的技巧用于两阶段的训练算法中，这样，就可以用 non-aligned 训练数据。在训练时同时推断未知的位置 $X_m$，尺度 $S_m$，方向 $A_m$. 极大化对数似然函数：
$$
\sum_{m=1}^{M} L\left(\mathbf{I}_{m} | \mathbf{B}_{X_{m}, S_{m}, A_{m}}, \lambda\right)
$$
假设有了足够好的推断，那么就可以从 non-aligned 数据中获得 aligned 数据。

因此算法就变成：

1. 给定 $\left\{\left(X_{m}, S_{m}, A_{m}\right), m=1, \ldots, M\right\}$，对数据 $\left\{\mathbf{I}_{m}\left[\operatorname{box}\left(X_{m}, S_{m}, A_{m}\right)\right], m=1, \ldots, M\right\}$ 进行两阶段的 sparse FRAME 训练。
2. 用2.4的检测方法推断新的 $\left\{\left(X_{m}, S_{m}, A_{m}\right), m=1, \ldots, M\right\}$

甚至也可以把对称变换考虑进来。

> 思考：神经网络就是要把这些东西都直接统一起来了。需要思考的是用什么样的方式将他们组织起来，以及用神经网络实现这些。

##### 2.6 Clustering

如何数据是多类别混在一起。要引入分类的变量。

##### 2.7 Learning deformable-part sparse FRAME models

这部分讨论的是更复杂的做法。就是将不同区域的 deformable part-templates 单独对待。有各自的尺度、方向。不过这部分的算法说的没有那么详细。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-12%E4%B8%8B%E5%8D%883.48.05.png" style="zoom:50%;" />

> 思考：这和每个基函数的变动有什么区别呢？是说这样对于基函数的限制要更小吗？

##### 2.8 Unsupervised codebook learning

简单来说就是不知一个 template，而是多个 template。所以称作 codebook。
$$
\mathbf{I}_{m}=\sum_{k=1}^{K_{m}} C_{m, k} \mathbf{B}_{X_{m, k}}^{\left(t_{m, k}\right)}+\epsilon_{m}
$$
但是基本思想都是基于 sparse FRAME。

#### Chapter 6 Hopﬁeld Auto-Encoder

##### 6.3 Hopﬁeld auto-encoder for sparse FRAME

**The local minima of FRAME are represented by an auto-encoder**

inhomogeneous FRMAE model
$$
p(\mathbf{I} ; w)=\frac{1}{Z(w)} \exp \left[\sum_{k, x} w_{k, x} h\left(\left\langle\mathbf{I}, B_{k, x}\right\rangle\right)\right] q(\mathbf{I})
$$
设 $q(\mathbf{I})$ 的均值为0，方差为1，能量函数为
$$
U(\mathbf{I})=\frac{1}{2}\|\mathbf{I}\|^{2}-\sum_{k, x} w_{k, x} h\left(\left\langle\mathbf{I}, B_{k, x}\right\rangle\right)
$$
极小化能量函数的解是满足
$$
\tilde{\mathbf{I}}=\sum_{k, x} w_{k, x} h^{\prime}\left(\left\langle\tilde{\mathbf{I}}, B_{k, x}\right\rangle\right) B_{k, x}
$$
这对应一个自编码器。
$$
\begin{array}{l}{c_{k, x}=w_{k, x} h^{\prime}\left(\left\langle\tilde{\mathbf{I}}, B_{k, x}\right\rangle\right)} \\ {\tilde{\mathbf{I}}=\sum_{k, x} c_{k, x} B_{k, x}}\end{array}
$$

##### 6.4 Hierarchical Hopﬁeld auto-encoder for generative ConvNet

**Learning the prototype models by Hopﬁeld auto-encoder**

对于 prototype model，能量函数为：
$$
\begin{aligned} U(\mathbf{I} ; w) &=-\sum_{k=1}^{K} h\left(\left\langle\mathbf{I}, \mathbf{w}_{k}\right\rangle+ b_{k}\right)+\frac{\|\mathbf{I}\|^{2}}{2} \\ &=-\sum_{k=1}^{K} \delta_{k}(\mathbf{I} ; w)\left(\left\langle\mathbf{I}, \mathbf{w}_{k}\right\rangle+ b_{k}\right)+\frac{\|\mathbf{I}\|^{2}}{2}\\
&=-\frac{1}{2}\left\|\mathbf{I}-\sum_{k=1}^{K} \delta_{k} \mathbf{w}_{k}\right\|^{2}+\frac{1}{2}\left\|\sum_{k=1}^{k} \delta_{k} \mathbf{w}_{k}\right\|^{2}-\sum_{k=1}^{K} \delta_{k} b_{k}
\end{aligned}
$$
如果考虑自编码器的重建误差：
$$
R(w)=\frac{1}{2 M} \sum_{m=1}^{M}\left\|\mathbf{I}_{m}-\sum_{k=1}^{K} \delta_{k}\left(\mathbf{I}_{m} ; w\right) \mathbf{w}_{k}\right\|^{2}
$$

$$
\frac{\partial R(w)}{\partial w}=\frac{1}{M}\left(\mathbf{I}_{m}-\sum_{k=1}^{K} \delta_{k}\left(\mathbf{I}_{m} ; w\right) \mathbf{w}_{k}\right) \delta_{k}\left(\mathbf{I}_{m} ; w\right)
$$

对于偏置项的导数几乎是 0，但是可以用偏置项来控制稀疏性：$s_{k}=\frac{1}{M} \sum_{m=1}^{M} \delta_{k}\left(\mathbf{I}_{m} ; w\right)$. 第一步：固定 $b_k$，更新 $\mathbf{w}_k$；第二步：给定 $\mathbf{w}_k$，更新 $b_k$ 使得 $s_k = \alpha$.

**Learning generative ConvNet by hierarchical Hopﬁeld auto-encoder**

在给定图像空间  $A(\delta ; w)=\{\mathbf{I}: \delta(\mathbf{I} ; w)=\delta\}$ 能量函数为：
$$
\begin{aligned} U(\mathbf{I} ; w) &=-\hat{\alpha}_{w, \delta}-\left\langle\mathbf{I}, \mathbf{B}_{w, \delta}\right\rangle+\frac{\|\mathbf{I}\|^{2}}{2} \\ &=\frac{1}{2}\left\|\mathbf{I}-\mathbf{B}_{w, \delta}\right\|^{2}-\frac{1}{2}\left\|\mathbf{B}_{w, \delta}\right\|^{2}-\alpha_{w, \delta} \end{aligned}
$$
重建误差为：
$$
R(w)=\frac{1}{2 M} \sum_{m=1}^{M}\left\|\mathbf{I}_{m}-\mathbf{B}_{w, \delta\left(\mathbf{I}_{m} ; w\right)}\right\|^{2}
$$
导数为：
$$
R^{\prime}(w)=\frac{1}{M} \sum_{m=1}^{M}\left(\mathbf{I}_{m}-\mathbf{B}_{w, \delta\left(\mathbf{I}_{m} ; w\right)}\right)^{\top} \frac{\partial}{\partial w} \mathbf{B}_{w, \delta\left(\mathbf{I}_{m ; w}\right)}
$$
对数似然函数的导数为：
$$
L^{\prime}(w) \approx \frac{1}{M} \sum_{m=1}^{M} \frac{\partial}{\partial w} f\left(\mathbf{I}_{m} ; w\right)-\frac{1}{\tilde{M}} \sum_{m=1}^{\tilde{M}} \frac{\partial}{\partial w} f\left(\tilde{\mathbf{I}}_{m} ; w\right)
$$
两者非常相似，只要用 $\tilde{\mathbf{I}}_{m}=\mathbf{B}_{w, \delta\left(\mathbf{I}_{m} ; w\right)}$ 替换，并且 假设 $\delta\left(\tilde{\mathbf{I}}_{m} ; w\right)=\delta\left(\mathbf{I}_{m} ; w\right)$，那么 $L^{\prime}(w) =R^{\prime}(w) $

### Bibtex:

```latex
@phdthesis{xie2016generative,
  title={Generative Modeling and Unsupervised Learning in Computer Vision},
  author={Xie, Jianwen Xie},
  year={2016},
  school={UCLA}
}
```

