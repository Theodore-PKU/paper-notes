# A Style-Based Generator Architecture for Generative Adversarial Networks

[TOC]

## 信息

Authors: Tero Karras, Samuli Laine, Timo Aila

Year: 2019 CVPR

Bibtex:

```latex
@inproceedings{karras2019style,
  title={A style-based generator architecture for generative adversarial networks},
  author={Karras, Tero and Laine, Samuli and Aila, Timo},
  booktitle={Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition},
  pages={4401--4410},
  year={2019}
}
```

[Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/a-style-based-generative-architecture-for-generative-adversarial-networks.pdf)



## Code

https://github.com/NVlabs/stylegan



## 概括

这篇文章从风格迁移的方法借鉴了生成器的思路，构造了一个 Style-Based 的生成器，因此 GAN 成为 styleGAN。主要的创新是在生成器的网络结构设计上，和判别器、损失函数、正则项等其他技巧无关。

styleGAN 的生成器的结构和以往的 GAN 结构不同（这篇文章提到的渐进式 GAN 是 baseline，这种结构的生成器的 input 是 latent variable，然后一级一级生成图像），它的主干网络和以往的生成器差不多，但是输入是不是 latent variable，而是一个常数。latent variable 其实有两个部分，一个控制随机性，一个控制图像的 style。随机性的部分通过在每一层的输出后面加上一个 noise 实现。style 的部分则是从一个 latent variable 出发，经过一个多层全连接网络得到一个新的隐变量，这个隐变量再经过不同的 affine transform，得到不同层的 scale 和 bias 参数。这些参数用来对主干网络的不同层进行 normalization（也就是更新 scale 和 bias）。

这个网络结构非常值得深思，它的很多实验结果也有启发性。



## 方法

styleGAN 的网络结构在图示中说明得很清楚。

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-09 下午6.58.46.png" alt="屏幕快照 2021-03-09 下午6.58.46" style="zoom:50%;" />

在这个图中，左侧是传统的 GAN 生成器，右侧是 styleGAN。两者有诸多区别。

1. styleGAN 的输入是一个 constant 4x4x512，而一般的做法是一个 latent variable，所有的随机性都来自这里。

2. latent variable 用来控制 style。$\mathbb{z}$ 经过一个 8层的 MLP 网络得到 $\mathbb{w}$，然后经过仿射变换，也就是图中的模块 A 得到 AdaIN 模块的参数。$\mathbb{z}, \mathbb{w}$ 和仿射变换的输出都是一维向量。AdaIN 的参数是该通道的均值和 bias。具体形式为：
   $$
   \begin{equation}
   \operatorname{AdaIN}\left(\mathbf{x}_{i}, \mathbf{y}\right)=\mathbf{y}_{s, i} \frac{\mathbf{x}_{i}-\mu\left(\mathbf{x}_{i}\right)}{\sigma\left(\mathbf{x}_{i}\right)}+\mathbf{y}_{b, i}
   \end{equation}
   $$
   这里的 $y$ 就是 A 模块的输出。而 $x_i$ 是某一层的某一通道。$y$ 的长度就是通道数的两倍（因为有均值和 bias）

3. 每个卷积输出都有一个 noise，这个noise随机产生之后，乘上一个 learned per-channel 参数，也就是 B 模块的作用，scale 之后的 noise 会加到卷积的输出上。这构成了另一种随机性。

最后需要指出的是一个 truncate 的技巧。作者对于变换后的隐变量 $\mathcal{W}$ 会施加一个 truncate 操作，目的是将 latent variable sample 范围限制在某个区域内。作者表示这样不容易出现在概率低的区域采样时产生不好的效果，但是一定程度上也要付出减少 variance 的代价。这个技巧在数学上可以表示为：

1. 先计算出平均值

$$
\begin{equation}
\overline{\mathbf{w}}=\mathbb{E}_{\mathbf{z} \sim P(\mathbf{z})}[f(\mathbf{z})]
\end{equation}
$$

2. 然后再采样 $\mathbf{w}$ 后，计算

$$
\begin{equation}
\mathbf{w}^{\prime}=\overline{\mathbf{w}}+\psi(\mathbf{w}-\overline{\mathbf{w}})
\end{equation}
$$

这里的 $\psi$ 就是用来控制采样的范围。



## 实验

这篇文章的实验对象是人脸数据，作者提供了一个新的高清人脸数据。FlickrFaces-HQ (FFHQ), 包含 70000 张 1024x1024 的人脸图像。https://github.com/NVlabs/ffhq-dataset

由于 latent space 不再是主干网络的输入，因此作者设计很多实验来说明新的 latent variable 的性质。

1. $\mathbb{z}$ 对于输出的影响是均值和bias，所以影响的是整个图像的 style，特别是在 finer 的部分，并不会改变细节，而是控制整体的风格。这也就是为什么是 styleGAN 的原因。不同 level 的 $\mathbb{w}$ 对图像的影响是不同的。如果有两个不同的 source，得到不同的 $\mathbb{w}$，进行特别的组合以后，生成的图像将会在不同的尺度上具有两个 source 的特征。总的来说，coarser 控制的是整体结构，finer 控制的是局部的结构和最终的色调。 
2. noise 部分控制的纹理，因为这个噪声是直接加在卷积的输出上。不同level 控制了不同程度的纹理。coarse 的纹理尺度大一下，finer的纹理尺度小一些。
3. 如果 $\mathbb{z}$ 部分规定，那么生成的图像都是在局部细节上有差异。



## 反思

这篇文章的 noise 设计让我想到了之前的 one-slice gan。后者可能也是类似的想法。

latent variable 在这篇文章里其实是分成了两个部分，一个是 $z$ 一个是 noise，因为 $z$ 控制的均值和方差，两者在数学上似乎是可以合并的。这样以来就和 heirarchical 的 latent variable 是类似的了。不过这里的均值和方差也是随机产生的，而不是像 VAE 那样是网络的输出。另外一个问题是，不同层 $z$ 的随机性来源是一样的，这个和一般的做法有差别。

不过这种 latent variable 和 NVAE 的相似性似乎说明 GAN 和 VAE 在网络结构的设计上也是有相通之处的。

关于 $z$ 到 $w$ 的一个 MLP，这个网络更像是 flow 模型那样，或者说将 latent variable 从一个标准的分布变成了一个新的分布。换言之，其实我们没有必要对隐变量的分布有太多的控制。只要它的 sample 是稳定的，即使它的分布不是高斯或者其他类型，也是完全没有问题的。这一点也许在 VAE 里也是一样。