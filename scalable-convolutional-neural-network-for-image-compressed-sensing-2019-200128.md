### Paper:

Scalable Convolutional Neural Network for Image Compressed Sensing

### Author:

Wuzhen Shi, Feng Jiang, Shaohui Liu, and Debin Zhao

### Year:

2019

### Notes:

阅读时间：2020.01.28

精读。这篇文章研究的是CS的图像恢复。作者的着眼点在于如何使用一个网络来训练一个 sampling rate 可变的网络，也就是对不同的 sampling rate，都可以使用同一个网络来重建。作者的基本想法是将 measurements 分成不同的组，最重要的是用来重建图像的主要部分（BL 模块），其余的是重建 residual （EL 模块）。

测量值部分使用的是 stride 很大的 non-overlapping 的block 卷积。BL 和 EL 部分的结构分成两个部分，第一个部分是 1x1 卷积 + reshape + block 拼接，构成 initial reconstruction，对于 EL 部分而言，会再加上前一层的 initial reconstruction；第二部分是一个 refine network，其结构在示意图中说明了。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-28%E4%B8%8B%E5%8D%882.01.22.png"/>

loss function 用的是 MSE，不过在论文中，似乎对 initial reconstruction 和 final reconstruction 都包含了。

如何实现可变的 sampling rate？作者的做法是通过对测量值进行分组，有多少测量值就用相对应的 BL、EL。这里有一个问题是 refine networks 是一样的吗？还是每一个 EL 都对应一个网络？这些论文中都没写。如果 sampling ratio 刚好卡在 两层 EL 或 BL 之间，就把多余的一部分 measurement 去掉，去掉的是不重要的。

本文最大的问题是，没有把训练的过程和测量值分组的部分写清楚。作者使用的是 greedy 的算法（也就是逐步改变测量值的index）来分组，组内测量值的重要性也是用 greedy 的算法分组。但是这种 greedy 的算法如何保证 initial reconstruction 部分的1x1卷积可以继续进行运算，这些用于测量的卷积又是如何训练的？先训练完再排序？这些问题都没有说清楚。尽管如此，可变 sampling rate 的思路还是很有趣的。

### Bibtex:

```latex
@inproceedings{shi2019scalable,
  title={Scalable Convolutional Neural Network for Image Compressed Sensing},
  author={Shi, Wuzhen and Jiang, Feng and Liu, Shaohui and Zhao, Debin},
  booktitle={Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition},
  pages={12290--12299},
  year={2019}
}
```

