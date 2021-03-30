# Conditional Adversarial Generative Flow for Controllable Image Synthesis

[TOC]

## 信息

Authors: Rui Liu, Yu Liu, Xinyu Gong, Xiaogang Wang, Hongsheng Li

Year: CVPR 2019

Bibtex:

```
@inproceedings{liu2019conditional,
  title={Conditional adversarial generative flow for controllable image synthesis},
  author={Liu, Rui and Liu, Yu and Gong, Xinyu and Wang, Xiaogang and Li, Hongsheng},
  booktitle={Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition},
  pages={7992--8001},
  year={2019}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/conditional-adversarial-generative-flow-for-controllable-image-synthesis.pdf)

cite: 16



## 概括

这篇文章利用 flow 模型设计了一个 coditional 生成模型。这里的条件指的是生成图像的不同属性（不仅仅是类别，类别相当于一个总体属性）。作者的想法是将条件映射到隐变量，用 flow 模型从隐变量生成图像。条件到隐变量的生成，作者采用了类似于条件 GAN 的训练方式，也用了 VAE 的训练方式，综合得到一个复杂的模型。具体来说，有一个 encoder 生成 z，用判别器判别，同时有一个 decoder 从 z 重建条件，还有一个分类器对 z 分类（类别就是条件）。而隐变量到图像的 flow 模型采用的 glow 模型。

在训练的时候，glow 模型先经过预训练。然后剩下的部分再一起训练。另外作者还增加了一个隐变量上的约束项。总而言之非常复杂。我是不会参考这样的模型的。更别说效果也不够惊艳。



## 方法

![屏幕快照 2021-03-30 下午2.16.31](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-30 下午2.16.31.png)

这个图基本上说明了这篇文章的思路。

这里需要说明的是 $c_s, c_u$，这两个条件变量是有区别的，因为对于人脸的生成，属性很多，可能仅仅指定其中一部分属性，这就是 $c_s$，剩下未指定的条件就是 $c_u$，这个部分在训练的时候是随机生成的，而 $\epsilon$ 则充当了隐变量生成时的随机性。监督模块有三个部分，目的就是让 z 的生成是合理的。但是这么复杂我不知道是不是很有必要。生成的时候很简单，只要从条件生成隐变量，传到 flow 模型就可以了。

另一方面，作者为了给出损失函数的合理性，还费了一方心思去推导，但是很多推导都是不必要的，这里我就不详细说明了。主要集中在 3.1 节。我认为这个推导完全是凑出来的，理论上没有足够的直觉来支撑这个推导。而且有一步莫名其妙地多出来一个东西。

额外需要说明的是，这里的分类器，是在此前的一些条件GAN 的文章中使用的方法。很奇怪的是这个方法融合了 VAE 和 GAN，因为生成的目的是 z 而不是条件，所以也说的通。监督模块的三个模型，采用了共享参数的策略，只有最后的输出是不同的。

之所以题目说是 controllable，是因为属性是可控的，所以称之为可控的图像合成。