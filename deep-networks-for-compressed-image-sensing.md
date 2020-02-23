### Paper:

Deep Networks for Compressed Image Sensing

### Author:

Wuzhen Shi, Feng Jiang, Shengping Zhang and Debin Zhao

### Year:

2017

### Notes:

这篇文章考虑的是压缩感知重建问题中的采样和重建两个部分。作者的想法是把这两个部分都用神经网络来表示。作者设计的网络框架是很常见的三个部分，1. 采样用的卷积层；2. 初步重建的卷积层；3. 多层卷积层重建生成信号。

和此前的一些文章不同，作者从 block 压缩感知的角度出发，对于sampling 的设计是基于 block 的卷积层。具体来说就是对于 BxB 大小的区域做卷积运算，stride=B，用这样的方式作为测量矩阵。这样的好处是速度快，如果把这个卷积层拆开看，就是一个block类型的测量矩阵，但是作者的输入仍然可以是整张图像，对于之后的重建都可以建立在整张图像上，而不是像block CS 那样所有的处理都基于 patch。

重建的卷积则是 1x1 的卷积，通道数则是 $B^2$，然后通过将 $B^2\times1$ 的向量转成 $B\times B$ 的block。实际上就相当于 block 情况下的一个测量矩阵的转置。

这两个卷积层都没有偏置。最后就是普通的多层卷积层。网络结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-27%E4%B8%8B%E5%8D%882.28.21.png" style="zoom:50%;" />

总结起来，可以看成在测量和初步重建的时候都是基于 patch 的（而且不重叠），但是最后重建的时候把 patch 都拼起来再过一个卷积网络。

### Bibtex:

```latex
@inproceedings{shi2017deep,
  title={Deep networks for compressed image sensing},
  author={Shi, Wuzhen and Jiang, Feng and Zhang, Shengping and Zhao, Debin},
  booktitle={2017 IEEE International Conference on Multimedia and Expo (ICME)},
  pages={877--882},
  year={2017},
  organization={IEEE}
}
```