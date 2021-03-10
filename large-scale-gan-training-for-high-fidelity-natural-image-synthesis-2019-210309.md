# Large Scale GAN Training for High Fidelity Natural Image Synthesis

[TOC]

## 信息

Authors: Andrew Brock, Jeff Donahue, Karen Simonyan

Year: ICLR 2019

Bibtex:

```
@article{brock2018large,
  title={Large scale GAN training for high fidelity natural image synthesis},
  author={Brock, Andrew and Donahue, Jeff and Simonyan, Karen},
  journal={arXiv preprint arXiv:1809.11096},
  year={2018}
}
```

[Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/large-scale-gan-training-for-high-fidelity-natural-image-synthesis.pdf)



## Code

https://tfhub.dev/s?q=biggan



## 概括

这篇文章研究的是 conditional GAN，模型名为 BigGAN 以及 BigGAN-deep。在这篇文章中，作者的贡献主要有以下几点：

1. 使用更大的网络、更大的 batch size 可以提升效果。
2. BigGAN 和 BigGAN-deep 的网络结构创新。
3. 使用了正交正则化和一个 truncated 技巧。

第一点贡献我们是无法借鉴的，因此关注的是第二点和第三点贡献。



## 方法

先讨论网络结构。作者提出了两个网络，一个是 BigGAN，一个是 BigGAN-deep。

BigGAN 的示意图：

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-10 上午10.21.28.png" alt="屏幕快照 2021-03-10 上午10.21.28" style="zoom: 50%;" />

因为是 conditional GAN，所以类别信息和隐变量z融合之后再经过线性变换传递到网络中。这里有两个部分，第一个是作为主干网络的起始输入。文中提到的 z 的维度并不大，但是 ch 通道数的值很大，所以输入的 4x4x16ch 这个张量有很大的线性性质。这里的 split 指的是将 z 分出不同的部分，每个部分参与不同 level 的 concat。实际上就是每一层有一个单独的随机 latent variable。除了主干网络的输入，另一部分的 latent variable 实际是作用于 BN 层的。具体如何作用在 BN 层上，根据论文的说法，我的理解是作为 scale 和 bias（但是这里的 scale 似乎是附加在原来的 BN 层基础上，是一个修正性质的参数，因此作者说是 gain 和 bias，而且 gain 是 1-centered，bias 是 0-centered）。和 styleGAN 的 AdaIN 是非常像的。

(b)(c) 是生成器和判别器的模块的具体设计。特别是生成器的模块设计，我觉得和 NVAE 非常接近。

BigGAN-deep 的示意图：

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-10 上午10.21.54.png" alt="屏幕快照 2021-03-10 上午10.21.54" style="zoom:50%;" />

在 BigGAN-deep 中，模型更深，latent variable 参与模型的方式也和 BigGAN 模型有一点区别。主要是在于这个模型没有对 z split，而是整个 z 和 conditional 信息融合之后，经过线性变换，不仅作为主干网络的输入，也作为后面 BN 层的 scale 和 bias。这种做法和 styleGAN 就更像了，因为 styleGAN 中的 $w$ 就是从相同的 z 出发。在这个模型中，生成器和判别器的模块变得更加复杂。

正交正则化。其数学表达式为：
$$
\begin{equation}
R_{\beta}(W)=\beta\left\|W^{\top} W \odot(\mathbf{1}-I)\right\|_{\mathrm{F}}^{2}
\end{equation}
$$
$W$ 是权重。这个正则化就和谱范数正则化很像，都是对权重的某种限制。

trucated normalizatin：这个其实非常简单，就是 z 采样之后，对于超出阈值范围的部分进行重采样直到所有的值满足为止。

文章还有其他的一些内容，但是我不太关注。



## 反思

这篇文章让人反思的主要是 BigGAN 模型和 styleGAN 模型其实有很多相似之处。特别是 latent variable 的处理。这种处理是否可以借鉴到 VAE 里呢？

另一个问题是模型大小。这篇文章的一个重要结论是模型越大越好，对于医疗图像数据，我们仅仅考虑一个单一类型的数据集，这一点还成立吗？