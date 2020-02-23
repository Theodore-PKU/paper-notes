### Paper:

Adaptive Sampling for Image Compressed Sensing Based on Deep Learning

### Author:

Liqun Zhong, Shuai Wan and Leyi Xie

### Year:

2019

### Notes:

阅读时间：2020.01.28

泛读。这篇文章研究的是压缩感知的 sampling 方式。作者的想法是根据图像的内容来调节采样的数量，因为是基于 block 的，所以有些 block 的采样多，有些 block 的采样少。作者此前有一篇文章 [Content-Adaptive Image Compressed Sensing Using Deep Learning](content-adaptive-image-compressed-sensing-using-deep-learning-2018-200110)，研究的是同样的内容，方法也几乎一样。区别在于，这篇文章，作者为了减少此前提出的算法的运行时间，提出在执行该算法之前，先执行一个预先的测量值数量分配。分配的方法是基于 saliency value（不知道怎么求的），在预先分配的基础上，再用之前的算法（逐步对一个 block 增加 5 个 测量值使得图像质量变得更好，所以作者针对不同数量的测量值训练了很多个网络来重建）来继续分配测量值。

每个 block 的测量值数量分配权重计算为：

<img src="http://latex.codecogs.com/svg.latex? \text {weight}_{i}=\sum_{h=1}^{B} \sum_{w=1}^{B}\left(s_{h 1, w 1}\right)^{p}\left(\sum_{h=1}^{H} \sum_{w=1}^{W}\left(s_{h, w}\right)^{p}\right)^{-1}" border="0"/>

预先分配时只会分配一部分的测量值，这个比例可调。

### Bibtex:

```latex
@inproceedings{zhong2019adaptive,
  title={Adaptive Sampling for Image Compressed Sensing Based on Deep Learning},
  author={Zhong, Liqun and Wan, Shuai and Xie, Leyi},
  booktitle={Journal of Physics: Conference Series},
  volume={1229},
  number={1},
  pages={012016},
  year={2019},
  organization={IOP Publishing}
}
```

