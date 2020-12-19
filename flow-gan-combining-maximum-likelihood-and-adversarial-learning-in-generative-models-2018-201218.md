# Flow-GAN: Combining Maximum Likelihood and Adversarial Learning in Generative Models

[TOC]

### 信息

Authors: Aditya Grover, Manik Dhar, Stefano Ermon

Stanford University

AAAI 2018

```latex
@inproceedings{grover2018flow,
  title={Flow-GAN: Combining Maximum Likelihood and Adversarial Learning in Generative Models},
  author={Grover, Aditya and Dhar, Manik and Ermon, Stefano},
  booktitle={AAAI},
  year={2018}
}
```

引用：68

[Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/flow-gan-combining-maximum-likelihood-and-adversarial-learning-in-generative-models.pdf)



### 概括

这篇文章提出使用对抗训练和极大似然概率的混合损失函数来训练一个基于 flow-based 模型的 GAN。简单地说，GAN 的生成网络是一个 flow-based 模型（NICE 或 Real NVP），判别器仍然存在。因为生成网络是 flow-based 模型，所以可以精确地计算概率，因此可以使用极大化似然函数的损失函数。这篇文章比较了在 flow-based 的生成模型基础上，使用 NLL 损失函数、对抗损失函数和混合损失函数的结果。结论是使用 NLL 损失函数的似然概率表现更好（作者表示，毕竟损失函数就是这个），而 ADV 的损失函数下图像的质量更好（使用了 MODE 这个指标），但是似然概率表现很差。而混合损失函数则兼顾了两者。

这篇文章的内容其实比较简单，但是也有一些问题：1. 一般的 GAN 模型是无法计算似然概率的，所以即使 flow-based 模型的似然概率表现差，也有可能是因为 flow-based 模型本身的问题。2. 文章中提到使用高斯混合模型可以得到很高的似然概率值，但是这种做法相当于记住了数据集。换句话说，在 flow-based 模型中，如何避免记住数据集，而真正实现对分布的模拟。3. 我想到 SRflow 的模型中，特征提取的部分是否非常重要，这一点原来的论文似乎没有提到。假如特征提取使用的结构较为简单，是否会导致 SRflow 的模型效果变差？4. 论文中提到如果概率模型不能表示真实的分布，其最终效果一定是变差的。所以图像的分布，用什么样的概率模型表示是最合适的，这个问题我认为是一个核心的问题。



### Code

https://github.com/ermongroup/flow-gan



### 方法和实验

这篇文章比较简单，基本上在概括中已经说明清楚了。

作者使用的数据集是 MNIST 和 CIFAR-10，非常简单，下图是不同的损失函数训练出来的结果：

![屏幕快照 2020-12-19 下午1.08.18](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-19 下午1.08.18.png)