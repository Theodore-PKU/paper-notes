# Deep Residual Fourier Transformation for Single Image Deblurring

[TOC]

## 信息

作者：Xintian Mao, Yiming Liu, Wei Shen, Qingli Li, Yan Wang

年份：2021

期刊文献：arXiv

Bibtex：

```latex
@article{mao2021deep,
  title={Deep Residual Fourier Transformation for Single Image Deblurring},
  author={Mao, Xintian and Liu, Yiming and Shen, Wei and Li, Qingli and Wang, Yan},
  journal={arXiv preprint arXiv:2111.11745},
  year={2021}
}
```

code：https://github.com/INVOKERer/DeepRFT

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/deep-residual-fourier-transformation-for-single-image-deblurring.pdf)



## 概括

略读。

这篇文章研究的是 debluring 的问题。基本的框架还是 network mapping。主要的内容是提出了一个新的 ResBlock 结构，将 FFT 考虑进来，目的是增强全局信息和关注低频部分（作者的解释）。作者将这个新的模块应用于 MIMO-Unet，这是一个多输入多输出的网络，多输入指的是不同尺度的图像都作为输入，多输出则是不同尺度的图像都输出（在不同的 level 上）。因为用了这个网络结构，所以损失函数复杂了一下，不过总的来讲还是基于 MSE 这样的损失函数。为了和 Sota 的结果比较，作者还替换了其中一些层的设计。作者表示实验结果优于 Sota。

我个人觉得这个方法没有什么特别的地方，考虑 FFT 的频域信息，可能实验效果会变好吧，但是我没有理解在 debluring 这个问题上，这么做的必要是什么。



## 方法

给出新模块的图示和 MIMO-Unet 的图示。

![屏幕快照 2021-12-01 上午3.49.06](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-12-01 上午3.49.06.png)

![屏幕快照 2021-12-01 上午3.49.20](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-12-01 上午3.49.20.png)

最后给出损失函数的设计：

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-12-01 上午3.59.09.png" alt="屏幕快照 2021-12-01 上午3.59.09" style="zoom:70%;" />

