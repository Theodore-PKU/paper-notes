# MSG-GAN: Multi-Scale Gradients for Generative Adversarial Networks

[TOC]

## 信息

Authors: Animesh Karnewar and Oliver Wang

Year: CVPR 2020

Bibtex:

```
@inproceedings{karnewar2020msg,
  title={Msg-gan: Multi-scale gradients for generative adversarial networks},
  author={Karnewar, Animesh and Wang, Oliver},
  booktitle={Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition},
  pages={7799--7808},
  year={2020}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/msg-gan-multi-scale-gradients-for-generative-adversarial-networks.pdf)



## Code

https://github.com/akanimax/msg-stylegan-tf



## 概括

GAN 有一个问题是训练不稳定。也就是说对于不同的数据集，训练参数是很敏感的。这篇文章的核心方法是 gradient at multi-scales，也就是要生成多尺度的图像，这些图像会通过 skip 连接传到判别器，作为中间层的额外的输入。这很类似于 ProGAN。但是这个方法相当于是在对于某一 GAN 网络的基础上进行修改，属于训练方法上的创新。实验表明这个方法对于不同数据集、不同分辨率、不同域的图像，甚至不同的损失函数、不同的网络结构，用相同的超参数，效果都很好。



## 方法

具体的示意图：

![屏幕快照 2021-03-17 上午9.04.43](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-17 上午9.04.43.png)

这个示意图采用的是 ProGAN 的骨架，也即是 DCGAN。

这种多尺度的结构和 styleGAN2 里提到的相似（这两篇文章相互引用，也是逗）。对于生成器，用 1x1 卷积把中间的特征图转换为某一分辨率的图像，对于判别器相当于生成器生成多个图像，这些图像通过 skip 连接到判别器，作为判别器的不同 scale 的输入。

理论支撑：有一篇文章提到训练不稳定和判别器的梯度有关。所以这篇文章的策略是通过多尺度的梯度来稳定训练。

相比较而言，progressive learning 的方法太麻烦了，超参数很多。这种方法可以理解成是用不同分辨率逐步训练 GAN 的不同层（包括生成器）

对于判别器，其特殊的网络结构可以用下列公式表示。
$$
\begin{equation}
\begin{aligned}
a_{j}^{\prime} &=d^{j}\left(\phi\left(o_{k-j}, a_{j-1}^{\prime}\right)\right) \\
&=d^{j}\left(\phi\left(o_{i}, a_{j-1}^{\prime}\right)\right)
\end{aligned}
\end{equation}
$$
$a$ 是判别器的特征输出，$o$ 是不同分辨率的图像，来自生成器的 skip 连接或者 real 图像的下采样

这里考虑了不同的信息融合的方式
$$
\begin{equation}
\begin{array}{l}
\phi_{\text {simple }}\left(x_{1}, x_{2}\right)=\left[x_{1} ; x_{2}\right] \\
\phi_{\text {lin_cat }}\left(x_{1}, x_{2}\right)=\left[r^{\prime}\left(x_{1}\right) ; x_{2}\right] \\
\phi_{\text {cat_lin }}\left(x_{1}, x_{2}\right)=r^{\prime}\left(\left[x_{1} ; x_{2}\right]\right)
\end{array}
\end{equation}
$$


## 实验

使用的数据集：CIFAR10 (60K images at 32x32 resolution); Oxford ﬂowers (8K images at 256x256), LSUN churches (126K images at 256x256), Indian Celebs (3K images at 256x256 resolution), CelebA-HQ (30K images at 1024x1024) and FFHQ (70K images at 1024x1024 resolution).

考虑两种基础网络：ProGAN 和 styleGAN，而且这两个网络使用的损失函数也不同，前者是 WGAN-GP loss，后者是 Non-saturating GAN loss with 1-sided GP。

在隐变量采样之后，使用了 hypersphere normalization 技术。在判别器中使用了 MinBatchStdDev 层，具体可以看 supplementary 的结构（但这个技巧是只有 GAN 模型才有的）

完全相同的超参数：学习率 0.003

实验结果：对于分辨率更高的图像，似乎 msg 的方法并不算太好。而且 styleGAN 还是比 ProGAN 更强。学习率的稳定性实验是基于 CIFAR-10 的，这里考虑的数据集有些太简单了，可信度不高。另外，不同的信息融合方式对于不同网路效果还不一样，对于 ProGAN 而言，simple 最好，对于 styleGAN 而言 cat_lin 最好。

作者认为 mixing regularization trik 可能是 MSG 不如 styleGAN 的原因。这个 trick 就是用不同的 z 生成不同的 w，然后用在不同尺度的 style 上。该技巧在 styleGAN 中被使用了，不记得 styleGAN2 在改进之后，这个技巧是否还能继续使用，这一点需要之后在梳理的时候确认一下。



## 反思

ProGAN 这篇文章还是要看一下，作为参考文献很重要。

比较在意的一个问题是，为什么不同的网络要用不同的信息融合方式。

还有一个问题是 GAN 模型中的哪些技巧对于 VAE 模型也是适用的？可能需要尝试。