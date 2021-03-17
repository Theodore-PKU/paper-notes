# Self-Attention Generative Adversarial Networks

[TOC]

## 信息

Authors:

Year: ICML 2019

Bibtex:

```latex
@inproceedings{zhang2019self,
  title={Self-attention generative adversarial networks},
  author={Zhang, Han and Goodfellow, Ian and Metaxas, Dimitris and Odena, Augustus},
  booktitle={International conference on machine learning},
  pages={7354--7363},
  year={2019},
  organization={PMLR}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/self-attention-generative-adversarial-networks.pdf)



## Code

https://github.com/brain-research/self-attention-gan



## 概括

这篇文章其实非常简单，就是将 self-attention 层用到了 GAN（具体来说是 contional GAN）。除了网络层的改变（这个结构不仅用于生成器，也用于判别器），还有 spectral normalization（同时应用于生成器和判别）和 TTUR 两个技巧。第一个是正则项，第二个是训练时的策略，使得训练更快。这篇文章在当时取得了 SOTA 的效果。



## 方法

self-attention 的示意图：

![屏幕快照 2021-03-17 下午6.55.49](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-17 下午6.55.49.png)

和原始的做法基本一样的。这里需要说的一个问题是 $f(x),g(x),h(x)$ 的通道数变化，作者表示，减少2/4/8倍效果都差不多，所以为了减少模型大小，使用了减少 8 倍。这有点像 SE？

数学公式是：
$$
\begin{equation}
\beta_{j, i}=\frac{\exp \left(s_{i j}\right)}{\sum_{i=1}^{N} \exp \left(s_{i j}\right)}, \text { where } s_{i j}=\boldsymbol{f}\left(\boldsymbol{x}_{\boldsymbol{i}}\right)^{T} \boldsymbol{g}\left(\boldsymbol{x}_{\boldsymbol{j}}\right)
\end{equation}
$$

$$
\begin{equation}
\boldsymbol{o}_{j}=\boldsymbol{v}\left(\sum_{i=1}^{N} \beta_{j, i} \boldsymbol{h}\left(\boldsymbol{x}_{\boldsymbol{i}}\right)\right), \boldsymbol{h}\left(\boldsymbol{x}_{\boldsymbol{i}}\right)=\boldsymbol{W}_{\boldsymbol{h}} \boldsymbol{x}_{\boldsymbol{i}}, \boldsymbol{v}\left(\boldsymbol{x}_{\boldsymbol{i}}\right)=\boldsymbol{W}_{\boldsymbol{v}} \boldsymbol{x}_{\boldsymbol{i}}
\end{equation}
$$

另外，作者用下式作为最终的输出：
$$
\begin{equation}
\boldsymbol{y}_{\boldsymbol{i}}=\gamma \boldsymbol{o}_{\boldsymbol{i}}+\boldsymbol{x}_{\boldsymbol{i}}
\end{equation}
$$
$\gamma$ 是可训练参数，训练最开始的时候，这个值是 0

实验的数据集是 ImageNet，分辨率为 128. 作者测试不同层（分辨率）使用 self-attention 层的效果，最终发现在 32/64 是比较有效的（只有一层用了）。我觉得这个和图像信息是在一幅图像中不同尺度的分布是有关的。作者也显示了 attention 层的 map 可视化，我感觉还不错。

![屏幕快照 2021-03-17 下午7.55.45](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-17 下午7.55.45.png)

最后还想说一个问题：作者试了baseline模型，因为改动了生成器和判别器的训练步数就导致 baseline 模型出现很大的问题，训练不稳定真的是 GAN 模型的大问题。