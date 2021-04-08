# NanoFlow: Scalable Normalizing Flows with Sublinear Parameter Complexity

[TOC]

## 信息

Authors: Sang-gil Lee, Sungwon Kim, Sungroh Yoon

Year: NeurIPS 2020

Bibtex:

```
@article{lee2020nanoflow,
  title={NanoFlow: Scalable Normalizing Flows with Sublinear Parameter Complexity},
  author={Lee, Sang-gil and Kim, Sungwon and Yoon, Sungroh},
  journal={arXiv preprint arXiv:2006.06280},
  year={2020}
}
```

cite: 2

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/nanoflow-scalable-normalizing-flows-with-sublinear-parameter-complexity.pdf)



## 概括

这篇文章的核心是如何减少 flow 模型的参数。作者的想法就是使用共享的参数。

最开始的想法是所有的变换都使用相同的参数，但是实际的结果不好，这个被称为 Nano-naive 模型。之后作者改为，每一次变换，最后的 1x1 卷积是不同参数，但其他部分是共享参数。最后，作者又增加了一个新的部分，称为 embedding 向量。这个向量可以看成是一个额外的参数，这个参数和每一层有关，不共享。而这一个向量参与网络的方式取决于使用的网络结构。作者提出了三种方式，分别是合并（和输入合并）、加法（channel-wise 的 bias）、乘法（一个channel 上的 scale 参数）。

作者有两个实验，一个实验室语音的实验，另一个是图像的实验。图像的实验中共享参数仅仅是相同的 scale 下的多个变换共享，不同scale 仍然是独立的。

这个方法看起来挺简单，但是不知道是不是真的有效。不过这篇文章倒是暴露的一个很大的问题，那就是 flow 模型的参数确实太多了，参数量和表达能力不成正比。



## 方法

这篇文章其实没有什么公式，这里仅仅放一个示意图。

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-08 下午1.59.26.png" alt="屏幕快照 2021-04-08 下午1.59.26" style="zoom:50%;" />



## 反思

我突然想到的一点是，如果 GAN，VAE，Flow 有共同之处，那么借鉴 GAN 模型的优点就应该非常关键的。styleGAN 之类的方法也许都可以有效地用来构建 Flow 模型。