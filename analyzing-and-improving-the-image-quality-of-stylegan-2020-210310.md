# Analyzing and Improving the Image Quality of StyleGAN

[TOC]

## 信息

Authors: Tero Karras, Samuli Laine, Miika Aittala, Janne Hellsten, Jaakko Lehtinen and Timo Aila

Year: CVPR 2020

Bibtex:

```
@inproceedings{karras2020analyzing,
  title={Analyzing and improving the image quality of stylegan},
  author={Karras, Tero and Laine, Samuli and Aittala, Miika and Hellsten, Janne and Lehtinen, Jaakko and Aila, Timo},
  booktitle={Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition},
  pages={8110--8119},
  year={2020}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/analyzing-and-improving-the-image-quality-of-stylegan.pdf)



## Code

https://github.com/NVlabs/stylegan2



## 概括

这篇文章是 styleGAN 的改进版。主要有三个方面。第一个是作者改进了 normalization 的计算形式，将其改动为对卷积层的参数的变动，目的是减少 droplet 伪影。第二个是增加了 PPL 正则项（PPL 是作者提出的一个度量 GAN 生成效果的指标，在 styleGAN 的文章中提出来的）。第三个是考虑 progressive 特点，对生成器和判别器的结构进行了一些修改，借鉴了渐进式的结构。另外，作者分析了生成不同分辨率时对最终图像的影响，根据这个分析修改了网络模型的大小。作者也测试发现更大的模型效果更好。最后，作者还提出了 styleGAN 从图像到 latent variable 的 inverse 计算。



## 方法

作者要解决的第一个问题是 droplet 伪影的问题。作者发现这个问题在64x64的特征图开始就容易出现，但是判别器没有检测出来。作者认为这个和 normalization 层有关，所以对原来 styleGAN 的这个计算进行了改动。原来的计算是从 latent variable z 计算出 normalization 层的 std 和 mean 之后，对归一化的卷积输出的每个 channel 计算，这个计算过程可以见下图的 (b)，主要包含两个部分，一个是 A 模块，一个是 B 模块。作者的改进则是将这两个模块的位置进行移动，得到 (c) 图。由于改动之后，mean 值不起作用，所以 A 模块仅仅对 std 起作用。而 (c) 图的操作，作者的实现方式是通过对卷积层的权重进行改动来实现的。分为 Mod 和 Demod 两个操作。
$$
\begin{equation}
w_{i j k}^{\prime}=s_{i} \cdot w_{i j k}
\end{equation}
$$

$$
\begin{equation}
w_{i j k}^{\prime \prime}=w_{i j k}^{\prime} / \sqrt{\sum_{i, k} w_{i j k}^{\prime}{ }^{2}+\epsilon}
\end{equation}
$$

第一个操作是修改了 std，第二个操作则是重新归一化。特别要注意的是 B 模块的位置和原来很不一样。

![屏幕快照 2021-03-10 下午6.24.23](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-10 下午6.24.23.png)

作者在论文中提到，这种对 weight 的标准化操作对于GAN训练是有益的。这个计算实际上和(c)图并不完全等价，但是因为这是从统计的假设出发的。(c) 的做法是完全 data-independent，但是 (d) 的做法则和 data 无关了。（不清楚实际上哪个更有用）。另外(d)的做法对激活函数也需要一定的修正，但具体怎么做不太清楚。

作者提出使用 PPL 正则项，这个还没完全搞懂，先不考虑它。这里可以说明的一点是，这个正则项计算起来比较麻烦，所以作者采用了一个策略，隔几个训练 batch 之后才在损失函数中计算这个正则项。

下面一个改进是关于网络结构的。原来的 styleGAN 没有考虑渐进式 GAN 的结构，但是这里借鉴了，原因是渐进式结构的训练稳定。当然渐进式的 GAN 也有一些问题（作者给了一个图示，牙齿的位置并没有随着人脸的位置变动而变动），作者认为这是和渐进式的训练设置有关。因此作者设计的渐进性结构和其他的做法不完全一样。 

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-10 下午6.32.57.png" alt="屏幕快照 2021-03-10 下午6.32.57" style="zoom: 67%;" />

这个图给出了三种结构。(a) 是其他人提出的结构，每个分辨率都输出图像，并且每个分辨率的输出通过 skip connection连接到判别器中。作者提出了(b)(c)两种做法。(b) 的做法是每个分辨率都输出图像，但是这些图像之间有上采样和残差的关系。在判别器中，这个中间输出最后回通过 skip connection传到判别器。左边支路的输出似乎就是最终的图像输出。(c)的做法则是在生成器和判别器的不同分辨率之间增加了一个残差操作。实验的结果是生成器采用 (b) 结构，判别器采用 (c)的结构比较好。

作者分析了不同分辨率的输出对最终图像的影响。实验的结果也很有意思。在训练之初，低分辨率对生成的图像影响很大。此后高分辨率的影响变大。并且作者发现1024分辨率的占比不高，所以推测在高分辨率的网络容量可以继续增加。换成一个更大的模型之后，1024分辨率的占比提升了。这个思路我认为非常有意思。

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-10 下午6.32.57-5374620.png" alt="屏幕快照 2021-03-10 下午6.32.57" style="zoom:50%;" />

最后作者给出了一个从图像到latent space 映射的计算方法。因为 styleGAN 中有 noise，所以和一般的 inverse 计算不同，作者对 noise 也进行优化。但是具体的优化方法和细节是在附录中，所以没有在论文中看到。



## 反思

这篇文章对 styleGAN 的改进，主要还是 normalization 部分和网络结构部分。normalization 部分的变动，我认为从直观上分析是有价值的。原来的方法中，噪声是在未归一化的情况下加上的，这有可能导致噪声在不同的局部有不一样的影响。改进之后则是归一化后加噪声，然后再用 A 模块改变 std，这样 B 和 A 综合的结果更像是加上了一个具有不同方差的噪声。这个实现我感觉和 VAE 更接近。网络结构的变动则更符合我对于生成模型的理解，换言之渐进式的生成是合理有效的。

另外对于不同分辨率图像和最终图像的影响，我觉得非常有价值，这个可以用来判断网络容量是否合理。也许有助于调整网络架构的参数。我们可以先用真实图像来计算这个分辨率影响的比例，再和生成的结果进行比较。