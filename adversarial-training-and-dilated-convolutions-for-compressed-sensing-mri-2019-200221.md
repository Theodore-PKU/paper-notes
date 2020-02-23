### Paper:

Adversarial Training and Dilated Convolutions for Compressed Sensing MRI

### Author:

Chao Xu a , Jinxu Tao, Zhongfu Ye, Jinzhang Xu b , Wajiha Kainat

### Year:

2019

### Notes:

阅读日期：2020.02.21

泛读。这篇文章研究的是 MRI 重建，用的方法是 GAN + multi-loss + dilated convolution + residual connection，简单说就是一个直接的网络重建，没有别的东西。损失函数包括对抗loss、图像域loss、kspace域loss、perceptual loss。residual connection 有两种，一种是短连接，一种是长连接。其他就没什么了。没啥参考价值。

给个网络结构图。

<img src="https://cdn.mathpix.com/snip/images/KZsvnSYOYKts96hWYtGjLty8mh_-VtQgqNY62MXawHM.original.fullsize.png" />

### Bibtex:

```latex
@inproceedings{xu2019adversarial,
  title={Adversarial training and dilated convolutions for compressed sensing MRI},
  author={Xu, Chao and Tao, Jinxu and Ye, Zhongfu and Xu, Jinzhang and Kainat, Wajiha},
  booktitle={Eleventh International Conference on Digital Image Processing (ICDIP 2019)},
  volume={11179},
  pages={111793T},
  year={2019},
  organization={International Society for Optics and Photonics}
}
```

