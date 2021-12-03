# Highly accelerated MR parametric mapping by undersampling the k-space and reducing the contrast number simultaneously with deep learning

[TOC]

## 信息

作者：挺多人的，懒得写了。

年份：2021

期刊会议：arXiv

Bibtex：暂无

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/highly-accelerated-mr-parametric-mapping-by-undersampling-the-k-space-and-reducing-the-contrast-number-simultaneously-with-deep-learning.pdf)



## 概括

略读。

这篇文章的主要内容是 MRI 重建。在 MRI 重建时，通常会同时扫描多个序列的数据进行重建，这就是所谓的 multi-contrast images. 当 contrast 数量越多，扫描的时间当然也就越长。因此作者考虑的重建方法是，现重建出两个 contrast 的图像，然后直接生成其他的 contrast 图像。因此模型包含两个部分，一个是重建网络，一个是生成网络。

重建部分作者采用了 ADMM-Net 的改进版本（我也不 care 这种方法了），损失函数是 NMSE，监督学习，直接 mapping。这个 ADMM-Net 的改进版本，每一轮迭代有好几个步骤，其中的三个算子都是用卷积层 + 激活层表示。

生成部分，作者则是直接用一个网络构造了不同 contrast 之间的映射，这也是用监督学习，损失函数是 2 范数。

我个人觉得这个方法没有什么特别的地方。因此略读，不看实验的细节了。

