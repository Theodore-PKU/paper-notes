## Estimating Uncertainty in Deep Learning MRI Reconstruction using a Pixel Classification Image Reconstruction Framework

[TOC]

### 信息

Authors: Kamlesh Pawar, Gary F Egan, and Zhaolin Chen

Year: 2021

Bibtex

```latex
@article{pawarestimating,
  title={Estimating Uncertainty in Deep Learning MRI Reconstruction using a Pixel Classification Image Reconstruction Framework},
  author={Pawar, Kamlesh and Egan, Gary F and Chen, Zhaolin}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/estimating-uncertainty-in-deep-learning-mri-reconstruction-using-a-pixel-classification-image-reconstruction-frameworkg.pdf)



### 概括

这篇文章研究的是 MRI 重建的不确定性。但是这里的不确定性似乎和我们所讨论的不确定性并不完全一致。基本的想法是将图像的灰度值看成是 256 类，重建任务变成一个分类任务，把每个像素的可能取之看成是 256 类中选择一个类。在这种建模下，不确定性指的是给定一个位置的像素，不同类的概率分布具有不确定性，每个类都有一定的概率，只是某些类的概率较高。训练的时候，则当作是一个分类任务来训练。这种不确定性，似乎也可以理解成模型对于其预测值（也就是重建的像素值的不确定性，即该模型并不是百分之百认为重建结果是这个像素值，也有一定概率是其他的像素值）。我个人更倾向于将这种不确定性称为模型的不确定，而不是重建了条件分布的不确定。



### 方法

正如概括中所说，把重建当作是一个分类任务。损失函数就是 cross-entropy。当网络训练之后，可以用下式来计算重建的像素值的大小（实际上就是加权平均或者说平均像素值）：
$$
I(r)=\frac{1}{N} \sum_{c=0}^{N-1} c \cdot p(r, c)
$$
这里的 r 表示位置，c 表示某一个灰度值，一共有 N = 256 中情形。p(r, c) 则是网络对于不同类的灰度值的预测概率。

而具体的 uncertainty map，作者用一个高斯分布去拟合网络预测出来的分类概率，拟合之后的高斯分布的方差作为 uncertainty map 的结果。

估计方法为：
$$
\min _{\sigma} \sum_{c=0}^{N-1}\left|\frac{1}{\sigma \sqrt{2 \pi}} \exp ^{-0.5((c-\mu) / \sigma)^{2}}-p(r, c)\right|
$$
这里的 $\mu$ 应该是先估计好的，应该就是 (1) 式的 I(r) 吧。



### 实验

使用的数据集是 fastmri 的 brain 数据。加速程度为 4。使用的网络是 U-Net，只是输出是 256 通道，表示 256 个类。

作者用一些图来说明这种估计的有效性。

1. 发现 tumor 部分的不确定性更大。
2. 重建结果和 label 的 error map 和 uncertainty map，都做一个阈值处理得到一个二值图像，比较这两个二值图像的 dice。
3. 计算 uncertainty map 的均值和一些图像质量指标 ssim/nmse 之间的相关性。

