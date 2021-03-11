# Progressive Growing of GANs for Improved Quality, Stability, and Variation

[TOC]

## 信息

Authors: Tero Karras, Timo Aila, Samuli Laine, Jaakko Lehtinen

Year: ICLR 2018

Bibtex

```
@article{karras2017progressive,
  title={Progressive growing of gans for improved quality, stability, and variation},
  author={Karras, Tero and Aila, Timo and Laine, Samuli and Lehtinen, Jaakko},
  journal={arXiv preprint arXiv:1710.10196},
  year={2017}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/progressive-growing-of-gans-for-improved-quality-stability-and-variation.pdf)



## Code

https://github.com/tkarras/progressive_growing_of_gans



## 概括

这篇文章提出了一种 GAN 的渐进式训练方法。这是最主要的内容。所谓渐进式的训练，就是生成和判别都是从小分辨率的图像开始，逐步增加网络层，生成和判别更大的分辨率，直至目标分辨率。这种训练方法可以加快训练，并且更稳定。网络结构并没有很特别的地方。



## 方法

其实文章里的两张图就足够说明这个方法了。

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-11 上午11.05.03.png" alt="屏幕快照 2021-03-11 上午11.05.03" style="zoom:50%;" />

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-11 上午11.05.12.png" alt="屏幕快照 2021-03-11 上午11.05.12" style="zoom:50%;" />

第一张图表示训练的时候，先训练生成低分辨率图像，判别也是如此。此时生成器的部分是浅层，而判别器是最深层的部分。

第二张图则增加了细节，而且给出了分辨率扩大时如何处理过渡时间的训练。首先，训练某一个分辨率的时候，生成器后面有一个 toRGB 模块，这个模块把 feature map 转换为图像，类似的在判别器里，有一个 fromRGB 模块，把图像转换成 feature map。在过渡期的时候，会有两个 toRGB 模块和两个 fromRGB 模块。这里的 $\alpha$ 可以控制多大比例使用前一个分辨率的图像。当 $\alpha$ 逐渐增加到 1 的时候，就变成完全训练新的分辨率。从这里的图式，似乎说明 to/fromRGB 模块都是和分辨率有关的，不同分辨率，对应的模块应该不同。另外训练的时候，小分辨率对应的网络层是会继续训练的。

这篇文章其实还提了一些其他的东西，但是我觉得都不太重要，关键还是所谓的渐进式训练。



## 反思

![屏幕快照 2021-03-11 上午11.11.39](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-11 上午11.11.39.png)

这个图给出了训练 1024 分辨率图像的网络。这里让我产生一个疑问，为什么 VAE 模型的 latent space 和 GAN 模型差不多大但是生成的图像确实模糊的。我试图给出这样的解释，在 VAE 里面，实际是对编码进行限制，但是编码本身可能没有包括足够多的图像信息，因此即使编码和先验的隐变量分布接近了，也无法实现对图像的很好复原，这个问题的本质是 AE 模型在没有 skip connection 的时候就会产生模糊的图像。而信息丢失是 AE 模型本身就存在的问题。但是在 GAN 模型中，不存在信息丢失这个概念，因为一开始就是从毫无限制的 latent variable 出发的。但是为什么这么小的隐变量可以生成足够好的图像呢？这是否可以解释为什么 GAN 模型有的时候缺少变化，因为随机性被固定了。图像增加的信息很多是在后面的网络里储存着。也因此越大的网络效果就会越好。这也反过来验证 styleGAN 的优异之处，它将随机性增加到了很多高分辨率的区域。

GAN 和 VAE 之间可能真的存在某种相互借鉴的关系。