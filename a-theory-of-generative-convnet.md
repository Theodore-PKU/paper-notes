### Paper:

A Theory of Generative ConvNet

### Author:

Jianwen Xie,  Yang Lu, Song-Chun Zhu, Ying Nian Wu

### Year:

2016

### Notes:

#### Abstract

这篇文章的主要内容是关于生成模型和判别模型的关系。作者表明一个生成随机场模型可以从一个多分类的判别模型中获得，只要假定其中一个类别是由 reference distribution 生成的。

如果进一步假设ReLU的非线性函数，并且 reference distribution 是高斯白噪声，那么这个生成模型是一个分片高斯模型，且分片的均值可以用一个自编码器定义。编码过程中的filter变成解码过程中的basis functions。激活函数的二值变量变成这组基下的系数。

可以用 Langevin dynamics 来采样。

#### Introduction

选择高斯白噪声作为 reference distribution 的原因：

> The reason we choose Gaussian white noise as the reference distribution is that it is the maximum entropy distribution with given marginal variance. Thus it is the most featureless distribution.

#### Generative ConvNet

令 $\mathbf{I}(x)$ 为一张图像，图像域为 $\mathcal{D}$，$x=(x_1,x_2)$ 表示坐标。多层卷积网络可以表达为：
$$
\left[F_{k}^{(l)} * \mathbf{I}\right](x)={h}\left(\sum_{i=1}^{N_{l-1}} \sum_{y \in \mathcal{S}_{l}} w_{i, y}^{(l, k)}\left[F_{i_{l i m}}^{(l-1)} * \mathbf{I}\right](x+y)+b_{l, k}\right)
$$
$l$ 表示层数，$\{F_k^{(l)},k=1,...,N_l\}$ 是第 $l$ 层的 filters，$\mathcal{S}_l$ 表示卷积核大小相关的坐标范围。第一层中，$[F_k^{0}*\mathbf{I}] = \mathbf{I}_k(x), k\in\{R,G,B\}$。如果有下采样，比如 max pooling 可以改写在卷积计算中。

一般来说可以把最后一层输出表示为一个 1x1 大小的向量。所以对于分类任务而言，设有 $C$ 类，每一类的 score function 可以写为：
$$
f_{c}(\mathbf{I} ; w)=\sum_{k=1}^{N_{\mathcal{L}}} w_{c, k}\left[F_{k}^{(\mathcal{L})} * \mathbf{I}\right]
$$
因此一个卷积网络的判别器就可以定义为：
$$
p(c | \mathbf{I} ; w)=\frac{\exp \left[f_{c}(\mathbf{I} ; w)+b_{c}\right]}{\sum_{c=1}^{C} \exp \left[f_{c}(\mathbf{I} ; w)+b_{c}\right]}
$$
其中，$w$ 包含了所有的参数。（在这个定义里，对 $f_c(\mathbf{I};w)$ 的形式实际上是没有限制的。）

利用贝叶斯公式可以推导出下式：
$$
p(\mathbf{I} | c ; w)=p_{c}({\mathbf{I}} ; w)=\frac{1}{Z_{c}(w)} \exp \left[f_{c}(\mathbf{I} ; w)\right] q(\mathbf{I})
$$
其中 $q(\mathbf{I})$ 是一个 reference distribution，也就是图像 $\mathbf{I}$ 的先验概率，论文中是直接定义这个式子为一个生成模型，且有命题：两个定义下的模型可以相互推导。但实际上可以看作是一个条件概率，不过省略了 $b_c$.

在另外一篇文章中的推导为（假定有一个类别，概率为 $q(\mathbf{I})$）：
$$
\frac{p(c | \mathbf{I} ; w)}{p(c=1 | \mathbf{I} ; w)}=\exp \left[f_{c}(\mathbf{I} ; w)+b_{c}\right]\\
\frac{p(c | \mathbf{I} ; w)}{p(c=1 | \mathbf{I} ; w)}=\frac{p(c, \mathbf{I} | w)}{p(c=1, \mathbf{I} | w)}=\frac{\rho_{c} p_{c}(\mathbf{I} ; w)}{\rho_{1} q(\mathbf{I})}
$$
如果只假设一个类别，并且使用卷积网络的形式，就得到一个生成模型：
$$
p(\mathbf{I} ; w)=\frac{1}{Z(w)} \exp \left[\sum_{k=1}^{K} \sum_{x \in \mathcal{D}_{L}}\left[F_{k}^{(L)} * \mathbf{I}\right](x)\right] q(\mathbf{I})
$$
对应的 scoring function 是：
$$
f(\mathbf{I} ; w)=\sum_{k=1}^{K} \sum_{x \in \mathcal{D}_{L}}\left[F_{k}^{(L)} * \mathbf{I}\right](x)
$$

#### A Prototype Model

一个简单版本。

假设图像域很小，比如 $10\times 10$，filters $w_k$ 本身属于图像域。一个单层网络就可以写成：
$$
p(\mathbf{I} ; w)=\frac{1}{Z(w)} \exp \left[\sum_{k=1}^{K} h\left(\left\langle\mathbf{I}, \mathbf{w}_{k}\right\rangle+ b_{k}\right)\right] q(\mathbf{I})
$$
其中 $h(r) =\max(r,0)$, $q(\mathbf{I})$ 是高斯白噪声：
$$
q(\mathbf{I})=\frac{1}{\left(2 \pi \sigma^{2}\right)^{|\mathcal{D}| / 2}} \exp \left[-\frac{1}{2 \sigma^{2}}\|\mathbf{I}\|^{2}\right]
$$
定义一个二值变量 $\delta_{k}(\mathbf{I} ; w)=1$ 如果 $\left\langle\mathbf{I},\mathbf{w}_{k}\right\rangle + b_k > 0$，否则为 0。记 $\delta(\mathbf{I} ; w)=\left(\delta_{k}(\mathbf{I} ; w), k=1, \ldots, K\right),$ $\delta=\left(\delta_{k}, k=1, \ldots, K\right)$.  这种表示相当于对图像空间进行了划分，用 $K$ 个超平面将空间分为 $2^K$ 个子空间。每一个图像 $\mathbf{I}$ 对应一个 $\delta(\mathbf{I}; w)$，可以依据 $\delta$，就可以确定该图像属于哪一个子空间 $A(\delta;w) = \{\mathbf{I}:\delta(\mathbf{I};w) =\delta\}$. 借助 $\delta$ 的记号，$h(r)$ 就变成 $\delta r$，就可以把原来的 $p(\mathbf{I} ; w)$ 写成：
$$
p(\mathbf{I} ; w, \delta) \propto \exp \left[\sum_{k=1}^{K} \delta_{k} b_{k}+\left\langle\mathbf{I}, \sum_{k=1}^{K} \delta_{k} \mathbf{w}_{k}\right\rangle-\frac{\|\mathbf{I}\|^{2}}{2}\right]\\
\propto \exp \left[-\frac{1}{2}\left\|\mathbf{I}-\sum_{k=1}^{K} \delta_{k} \mathbf{w}_{k}\right\|^{2}\right]
$$
对应的就是 $\mathrm{N}\left(\sum_{k} \delta_{k} \mathbf{w}_{k}, \mathbf{1}\right)$ 限制在每一个子空间 $A(\delta ; w)$ 上。这也看成一个自编码的过程：$\mathbf{I} \rightarrow \delta \rightarrow \sum_{k} \delta_{k} \mathbf{w}_{k}$.

#### Internal Structure of Generative ConvNet

回到卷积生成模型，$\mathbf{F}^{(l)} * \mathbf{I}$ 可以看成一个 3D 图像。每一个filter对应的 feature map 可以写为：
$$
\left[F_{k}^{(l)} * \mathbf{I}\right](x)=h\left(\left\langle\mathbf{w}_{k, x}^{(l)}, \mathbf{F}^{(l-1)} * \mathbf{I}\right\rangle+ b_{l, k}\right)
$$
其中，
$$
\left\langle\mathbf{w}_{k, x}^{(l)}, \mathbf{F}^{(l-1)} * \mathbf{I}\right\rangle=\sum_{i=1}^{N_{l-1}} \sum_{y \in \mathcal{S}_{l}} w_{i, y}^{(l, k)}\left[F_{i}^{(l-1)} * \mathbf{I}\right](x+y)
$$
类似地，定义一个二值激活变量：
$$
\delta_{k, x}^{(l)}(\mathbf{I} ; w)=1\left(\left\langle\mathbf{w}_{k, x}^{(l)}, \mathbf{F}^{(l-1)} * \mathbf{I}\right\rangle+ b_{l, k}>0\right)
$$
那么每一个 feature map 就可以写成：
$$
\left[F_{k}^{(l)} * \mathbf{I}\right](x)=\delta_{k, x}^{(l)}(\mathbf{I} ; w)\left(\left\langle\mathbf{w}_{k, x}^{(l)}, \mathbf{F}^{(l-1)} * \mathbf{I}\right\rangle+ b_{l, k}\right)
$$
于是，$\delta(\mathbf{I} ; w)=\left(\delta_{k, x}^{(l)}(\mathbf{I} ; w), \forall k, x, l\right)$ 就是图像 $\mathbf{I}$ 的激活模式。把激活函数用二值激活变量分离之后，就可以把 $f(\mathbf{I};w)$ 写成
$$
\begin{aligned} f(\mathbf{I} ; w) &=\alpha_{l}+\left\langle\mathbf{B}^{(l)}, \mathbf{F}^{(l)} * \mathbf{I}\right\rangle \\ &=\alpha_{l}+\sum_{k=1}^{N_{l}} \sum_{x \in \mathcal{D}_{l}} \mathbf{B}_{k}^{(l)}(x)\left[F_{k}^{(l)} * \mathbf{I}\right](x) \end{aligned}
$$
其中，
$$
\mathbf{B}^{(l-1)}=\sum_{k=1}^{N_{l}} \sum_{x \in \mathcal{D}_{l}} \mathbf{B}_{k}^{(l)}(x) \delta_{k, x}^{(l)}(\mathbf{I} ; w) \mathbf{w}_{k, x}^{(l)}
$$
也就是可以从  $\mathbf{B}^{(L)}$ 推出 $\mathbf{B} = \mathbf{B}^{(0)}$。

在 $\mathbf{B}$ 的递推中，$\mathbf{B}_{k}^{(l)}(x) \delta_{k, x}^{(l)}$ 就是基函数 $\mathbf{w}_{k, x}^{(l)}$  的系数。 

所以 score function 就会简化成 $f(\mathbf{I} ; w)=\alpha_{w, \delta}+\left\langle\mathbf{I}, \mathbf{B}_{w, \delta}\right\rangle$. 于是，概率分布式子就可以写成和简单版本一样的形式（piecewise Gaussian）：
$$
\begin{aligned} p(\mathbf{I} ; w, \delta) & \propto \exp \left[\alpha_{w, \delta}+\left\langle\mathbf{I}, \mathbf{B}_{w, \delta}\right\rangle-\frac{\|\mathbf{I}\|^{2}}{2}\right] \\ & \propto \exp \left[-\frac{1}{2}\left\|\mathbf{I}-\mathbf{B}_{w, \delta}\right\|^{2}\right] \end{aligned}
$$
同样的，$\mathrm{N}\left(\mathbf{B}_{w, \delta}, \mathbf{1}\right)$ 限制在 $A(\delta ; w)$. 也有一个相应的自编码过程：$\mathbf{I} \rightarrow \delta \rightarrow \mathbf{B}_{w, \delta}$. $\mathbf{B}_{w, \delta}$ 是子空间 $A(\delta ; w)$ 图像的估计（有一点像平均）。

#### Learning Generative ConvNet

作者在论文里说，从训练图像中学习 $w$，需要极大化似然函数，也就是 $\max L(w) = \sum^M_{m=1} \log p(\mathbf{I};w)/M$，似然函数对 $w$ 的导数是:
$$
\frac{\partial L(w)}{\partial w}=\frac{1}{M} \sum_{m=1}^{M} \frac{\partial}{\partial w} f\left(\mathbf{I}_{m} ; w\right)-\mathrm{E}_{p}\left[\frac{\partial}{\partial w} f(\mathbf{I} ; w)\right]
$$
推导的主要内容是
$$
\frac{\part{\log Z(w)}}{\part{w}} = \frac{1}{Z(w)}\int \exp \left[f(\mathbf{I} ; w)\right] q(\mathbf{I}) \frac{\part{f(\mathbf{I} ; w)}}{\part{w}}d \mathbf{I}\\
=\int \left(\frac{1}{Z(w)}\exp \left[f(\mathbf{I} ; w)\right] q(\mathbf{I}) \right)\frac{\part{f(\mathbf{I} ; w)}}{\part{w}}d \mathbf{I}\\
=\int p(\mathbf{I};w)\frac{\part{f(\mathbf{I} ; w)}}{\part{w}}d \mathbf{I}\\
=\mathrm{E}_{p}\left[\frac{\partial}{\partial w} f(\mathbf{I} ; w)\right]
$$
这一项使用生成的图像来估计。

从 $p(\mathbf{I};w)$ 采样的方法是 Langevin dynamics：
$$
\mathbf{I}_{\tau+1}=\mathbf{I}_{\tau}-\frac{\epsilon^{2}}{2}\left[\mathbf{I}_{\tau}-\mathbf{B}_{w, \delta\left(\mathbf{I}_{\tau} ; w\right)}\right]+\epsilon Z_{\tau}
$$
其中，$Z_{\tau} \sim \mathrm{N}(0, \mathbf{1})$.

给出的算法是：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-10%E4%B8%8B%E5%8D%886.15.04.png" style="zoom:50%;" />

算法中的 update $w$ 这一步和 GAN 的对抗 loss 有一些相似。因为要极大化 $f(\mathbf{I}_m;w)$，所以 $H^{obs}$ 的符号是正的，对于生成图像是负的，Langevin dynamics 这一步更新 $\mathbf{\tilde{I}}_m$ 就是使得生成的图像接近训练的分布。

### Bibtex:

```latex
@inproceedings{xie2016theory,
  title={A theory of generative convnet},
  author={Xie, Jianwen and Lu, Yang and Zhu, Song-Chun and Wu, Yingnian},
  booktitle={International Conference on Machine Learning},
  pages={2635--2644},
  year={2016}
}
```

Zeiler, Matthew D and Fergus, Rob. Visualizing and understanding convolutional neural networks. In ECCV, 2014.

Hinton, Geoffrey E. Training products of experts by minimizing contrastive divergence. Neural Computation, 14 (8):1771–1800, 2002.