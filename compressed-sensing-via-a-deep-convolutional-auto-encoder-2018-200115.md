### Paper:

Compressed Sensing via a Deep Convolutional Auto-encoder

### Author:

Hao Wu, Ziyang Zheng, Yong Li, Wenrui Dai, Hongkai Xiong

### Year:

2018

### Notes:

阅读日期：2020.01.15

泛读。这篇文章研究的是 CS 图像重建。测量矩阵在这篇文章中，作者使用的是一个 encoder（而且不带 BN 和激活函数），其余的网络结构是一个解码器（带 BN 和激活函数 ReLU，和编码器构成一个自编码器。解码器的输出作为中间值，再传入一个 dense net 得到 refine 的结果。

网络结构：

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-15%E4%B8%8B%E5%8D%888.59.24.png)

一些细节：测量值的数量由 encoder 的输出的通道数决定；encoder 用的是 4x4 卷积，stride=2。解码器输出和 dense net 的输出都有一个 2 范数的损失函数。

### Bibtex:

```latex
@inproceedings{wu2018compressed,
  title={Compressed Sensing via a Deep Convolutional Auto-encoder},
  author={Wu, Hao and Zheng, Ziyang and Li, Yong and Dai, Wenrui and Xiong, Hongkai},
  booktitle={2018 IEEE Visual Communications and Image Processing (VCIP)},
  pages={1--4},
  year={2018},
  organization={IEEE}
}
```

