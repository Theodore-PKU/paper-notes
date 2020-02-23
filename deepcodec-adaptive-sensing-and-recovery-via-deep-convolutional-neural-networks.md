### Paper:

DeepCodec: Adaptive Sensing and Recovery via Deep Convolutional Neural Networks

### Author:

Ali Mousavi, Gautam Dasarathy, Richard G. Baraniuk

### Year:

2017

### Notes:

这篇文章中，作者试图用网络来学习一种测量，而不是传统的用测量矩阵得到测量值，他们希望通过这种做法可以突破传统压缩感知理论对极小化l1范数时对于测量值数量的限制，也就是用更少的测量值恢复数据。

如果要用非线性的方法测量，因为测量值的数量少，所以一定要降维。作者回顾了在深度卷积网络中的降维的手段 pooling，认为作一般的pooling方法是手工设计的，所以不能很好的保留信息，作者提出的方法借鉴了 sub-pixel 卷积层。这个卷积层本来用在超分辨任务中，目的是增加维度。作者认为只需要修改这个操作就可以得到降维的效果。实际上，作者的做法是先对输入 rearange 得到测量值长度的，r 个channel 的输入（r = N/M），然后通过多个卷积层(size 不变)，最后一个卷积层的输出通道是1. 重建的时候就是反过来，把通道数从 1 加到 r。

> 对于图像而言，可以想到的方式就是先空间上resize，把像素在channel方向上叠起来，然后不断缩小channel。这种先 rearange 的方式和直接用卷积层，用很大的，但是 stride 很大有什么区别？？应该是一样的（在第一次卷积的时候）。

按照这种方式，最终得到的就是一个自编码模型，编码就是测量值。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-25%E4%B8%8B%E5%8D%8810.40.23.png" style="zoom:50%;" />

论文中，作者的比较对象是之前自己提出的DeepInverse和Lasso。感觉最大的问题是实验数据，用的是CIFAR-10的列经过小波稀疏变换之后的结果。

### Bibtex:

```latex
@article{mousavi2017deepcodec,
  title={Deepcodec: Adaptive sensing and recovery via deep convolutional neural networks},
  author={Mousavi, Ali and Dasarathy, Gautam and Baraniuk, Richard G},
  journal={arXiv preprint arXiv:1707.03386},
  year={2017}
}
```