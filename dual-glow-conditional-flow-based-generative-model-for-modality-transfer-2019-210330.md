# DUAL-GLOW: Conditional Flow-Based Generative Model for Modality Transfer

[TOC]

## 信息

Authors:

Year:

Bibtex:

```latex
@inproceedings{sun2019dual,
  title={Dual-glow: Conditional flow-based generative model for modality transfer},
  author={Sun, Haoliang and Mehta, Ronak and Zhou, Hao H and Huang, Zhichun and Johnson, Sterling C and Prabhakaran, Vivek and Singh, Vikas},
  booktitle={Proceedings of the IEEE/CVF International Conference on Computer Vision},
  pages={10611--10620},
  year={2019}
}
```

cite: 8

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/dual-glow-conditional-flow-based-generative-model-for-modality-transfer.pdf)



## 概括

这篇文章的研究对象是模态转换，具体的任务是医学图像，从 MRI 到 PET 的图像转换。作者的主要思路是用两个 glow （flow 模型) 来实现两者之间的转换。通常的方法有 AE 和 GAN 的方法。这篇文章的思路是通过网络将两个图像的隐变量关联起来，两个 glow 模型分别对应一种图像类型的建模。当我们要从 MRI 转换到 PET 时，首先用一个 glow 模型从 MRI 图像获得一个隐变量 z_m，然后用一个神经网络（作者在论文中没有说具体的网路结构）得到 PET 图像的隐变量分布 p(z_p|z_m)（这个分布作者把它当作是一个高斯分布），最后通过 PET 的 glow 模型从 z_p 生成出对应的 PET 图像。由于隐变量有 split 的结构，所以 p(z_p|z_m) 的部分其实是有很多个 level。整体的结构其实很类似于 AE 或者 U-Nnet，只不过说 encoder 和 decoder 都是 glow 模型，对应的损失函数是基于 glow 模型得出来的。关于训练的损失函数在下面的小节中具体说明。

除此之外，作者还考虑如果有额外的一些条件，怎么将其应用到图像转换中去。这个部分不是特别重要，我只大致了解了作者思路，没有关注细节的原理。作者的想法是额外的条件是 side information，然后这些信息只在 top level 的模块中出现。这里的 level 指的是 glow 模型的隐变量的 split 分层。而其他的隐变量部分是不包含 side information，为了实现这一点，作者增加了一些判别器和 GRL 模块，这个 GRL 模块不知道是啥。总之这个部分的基本想法就是这样的。



## Code

https://github.com/haolsun/dual-glow



## 方法

我们主要说明怎么训练。

原始的目标函数是：
$$
\begin{equation}
\begin{array}{l}
\log p_{\theta}\left(\mathbf{x}_{p} \mid \mathbf{x}_{m}\right)=\log \left(p_{\theta}\left(\mathbf{x}_{p}, \mathbf{x}_{m}\right) / p_{\theta}\left(\mathbf{x}_{m}\right)\right) \\
=\log p_{\theta}\left(\mathbf{z}_{p} \mid \mathbf{z}_{m}\right)+\log \left(\frac{\left|\operatorname{det}\left(d\left(\mathbf{z}_{p}, \mathbf{z}_{m}\right) / d\left(\mathbf{x}_{p}, \mathbf{x}_{m}\right)\right)\right|}{\left|\operatorname{det}\left(d \mathbf{z}_{m} / d \mathbf{x}_{m}\right)\right|}\right) \\
\quad=\log p_{\theta}\left(\mathbf{z}_{p} \mid \mathbf{z}_{m}\right)+\log \left(\left|\operatorname{det}\left(d \mathbf{z}_{p} / d \mathbf{x}_{p}\right)\right|\right)
\end{array}
\end{equation}
$$
目的还是训练一个条件概率，不过因为有 glow 模型的假设，会变成和隐变量有关的概率和 det of Jacobian. 但是在这个损失函数中，没有办法对 $z_m$ 进行限制，无法控制 $z_m$，实际上，这里的 MRI glow 模型充当的就是一个 encoder，获得编码就可以了。为了控制 MRI 图像的隐变量，作者增加了一项，也就是关于 MRI glow 模型本身，要满足一个好的生成模型。额外的这一项就是 $\begin{equation}
\lambda \log p_{\theta}\left(\mathbf{x}_{m}\right)
\end{equation}$, 就是 MRI glow 的生成 loss。作者加了一个参数 $\lambda$。我想到的是也可以先训练一个 MRI glow，只不过因为作者并不是真的需要一个关于 MRI 的生成模型，所以直接合并起来训练了吧。至于这个生成模型怎么样其实无所谓。

而 glow 模型基本上和原来的做法是一样的，所以网络结构上没有什么创新。

这篇文章的 glow 模型其实还不小，因为有 4 level，每个 level 有 16 个 affine coupling layer。coupling layer 中的参数网络是比较小的 3D 卷积网络。从实验的结果上来看这个方法比其他方法好很多。

![屏幕快照 2021-04-01 下午5.25.51](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-01 下午5.25.51.png)

