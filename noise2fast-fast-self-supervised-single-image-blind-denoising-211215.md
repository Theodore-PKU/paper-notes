# Noise2Fast: Fast Self-Supervised Single Image Blind Denoising

[TOC]

## 信息

作者：Jason Lequyer, Reuben Philip, Amit Sharma, Laurence Pelletier

年份：2021

期刊会议：arXiv

Bibtex:

```latex
@article{lequyer2021noise2fast,
  title={Noise2Fast: Fast self-supervised single image blind denoising},
  author={Lequyer, Jason and Philip, Reuben and Sharma, Amit and Pelletier, Laurence},
  journal={arXiv preprint arXiv:2108.10209},
  year={2021}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/noise2fast--fast-self-supervised-single-image-blind-denoising.pdf)

code: https://github.com/pelletierlab/Noise2Fast



## 概括

通读。

这篇文章研究的是自监督去噪。但是这篇文章并不是在整个 noisy 数据集上进行训练，而是每个噪声图像单独训练，类似于 DIP 的方法。只不过训练的方式和 DIP 大为不同。这篇文章的基本想法是通过 checkboard 形式的下采样，将一张图片变换成四张小图片，这四张小图片作为训练集来训练网络，输出和输入都是一样的，只是进行了组合，保证输出和输入并不相同。这个过程在方法的图中可以很容易理解。作者使用的网络是一个较为简单的小网络，因为数据集很小，作者不希望过拟合，因此也提出了一个 early stop 的方法，即在整个原图上做 validation，只要原图作为输入时，输出结果和原始图像的某个指标几乎不变了，就停止网络的训练。

从本质上说，这篇文章可以看成是将图片分成了 odd 和 even 两个部分，作者假设这两个部分实际上的隐含的真实图像是一样的，只是噪声不同，所以 odd 和 even 分别作为输入和输出，这样训练就有些类似于 Noise2Noise 了。

作者的方法是对一张图片进行训练的，但是比较对象却有很多是基于整个数据集训练的方法，我认为这个部分非常不合理。另外这种做法是很难应用定理一来分析的，因此不会特别关注，仅仅是了解一下这种做法。



## 方法

将图像变成 odd, even 的方法：

![屏幕快照 2021-12-15 上午10.08.21](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-12-15 上午10.08.21.png)

网络结构：

![屏幕快照 2021-12-15 上午10.13.58](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-12-15 上午10.13.58.png)

训练的流程：

![屏幕快照 2021-12-15 上午10.14.15](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-12-15 上午10.14.15.png)



