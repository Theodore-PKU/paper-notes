# Generative Probabilistic Image Colorization

[TOC]

## 信息

作者：Chie Furusawa, Shinya Kitaoka, Michael Li, Yuri Odagiri

期刊会议: arXiv

年份: 2021

Bibtex: 

```latex
@article{furusawa2021generative,
  title={Generative Probabilistic Image Colorization},
  author={Furusawa, Chie and Kitaoka, Shinya and Li, Michael and Odagiri, Yuri},
  journal={arXiv preprint arXiv:2109.14518},
  year={2021}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/generative-probabilistic-image-colorization.pdf)



## 概括

通读。

这篇文章将 ddpm 方法应用到图片上色的任务中。更具体地说，图片上色是对动漫的线稿图片进行上色。作者定义的扩散过程和原始 ddpm 没有区别，逆过程考虑了线稿的图片，作为条件之一。不过网络设计上较为复杂，线稿的图像会经过一个 encoder 得到特征，这些特征会输入到噪声预测的网络中。采样的方法也和 ddpm 有所区别，采用的是 SDE 的 PC sampler 方法。具体的训练和采样算法见方法部分。论文中的算法图将这一部分写得很清楚。线稿上色的问题并不是我们所关心的，因此并未对实验、数据集等进行详细整理。



## 方法

这篇文章中，假设原始图片是 $x$，那么对应的线稿图（通过一系列操作获得）记为 $\mathcal{L}(x)$。

扩散过程的定义如下：

![屏幕快照 2021-11-20 下午2.22.47](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-11-20 下午2.22.47.png)

可见和原始的 ddpm 并没有区别。

估计噪声的模型 $\epsilon_{\theta}$ 考虑了线稿的条件，因此也是网络的输入之一。不过网络结构较为复杂。下图的左侧是整体结构，右侧是 decoder 部分的细节。

![屏幕快照 2021-11-20 下午2.22.57](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-11-20 下午2.22.57.png)

线稿首先经过一个 encoder 得到一些特征之后再输入到 decoder 中，这里的decoder 实际上才是 ddpm 的 $\epsilon_{\theta}$. 在右图中可以看出，仍然是一个 U-Net 结构。只不过，线稿的特征也会传入。右图中的红色箭头表示关于时间的 positional embedding，这应该和其他 ddpm 模型差不多？

训练算法见下图。

![屏幕快照 2021-11-20 下午2.23.19](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-11-20 下午2.23.19.png)

这里的第四行给出了这篇文章的 noise level schedule 的形式。第六行则说明训练的时候使用的是 1 范数而不是 2 范数训练。这有点不一样，不过似乎不少文章都使用了 1 范数的训练。而且，这里对于时间 t 也不是完全离散的，而是连续化，从参数 $\xi$ 就可以看出。当然，这个参数设置仍然对应着不同的时刻 t，换句话说，当 $\xi = 1$ 的时候，对应的是 t = T 的情形。因此虽然训练是连续的，但是仍然存在离散的时刻 1, 2, ..., T 和它们对应。这样才可以实现采样算法。 

采样算法见下图。

![屏幕快照 2021-11-20 下午2.23.24](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-11-20 下午2.23.24.png)

这个算法分成了两个部分，Predictor 和 Corrector，这是 PC sampler 的方法。对于原始的 DDPM，仅有 Predictor 的部分，但是这里增加了 Corrector，说实话，是否有效是需要在实验中验证的（作者也做了相关的实验）。我们在设计采样算法的时候，也应该考虑这个问题。当然 Corrector 会增加采样的步数。所以这里的参数设置细节是需要考虑的。另外，实际上 $x_{T}$ 的采样并不是纯粹的高斯噪声。作者说他们使用了 bias 的初始值。具体来说就是有一个单色图像作为基础，再加上一个高斯噪声。两者的权重由 $\bar{\alpha}_{T}$ 决定。