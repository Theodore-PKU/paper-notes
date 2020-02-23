### Paper:

A New Pan-Sharpening Method With Deep Neural Networks

### Author:

Wei Huang, Student Member, IEEE, Liang Xiao, Member, IEEE, Zhihui Wei, Hongyi Liu, and Songze Tang

### Year:

2015

### Notes:

这是一篇应用文章（解决遥感图像领域中的分辨率问题），实际上和压缩感知并不相关，只是之前有人用压缩感知的方法来解决这个问题。

问题可以描述为：PAN图像是高分辨率的，MS图像低分辨率。用PAN图像的信息来重建高分辨率的MS图像。MS图像有多个band，线性组合的结果就是PAN图像。

方法：用一个多层全连接的神经网络。训练时用低分辨率的MS图像构建的PAN图像和高分辨率的PAN图像做训练。训练时用7x7patch。预训练是逐层进行，即第一层和最后一层构成的网络来训练。然后用第一层的输入来训练第二层和倒数第二层。依次进行。预训练的目的只是为了训练上的需要。最后对DNN进行fine-tuning。重建时用不同band的MS图像作为输入，输出就是高清的MS图像。因此这篇论文就是一个超分辨任务，和压缩感知无关。不过这种全连接的网络，由于是7x7的patch，所以和普通的卷积网络也没什么区别。

### Bibtex:

```latex
@article{huang2015new,
  title={A new pan-sharpening method with deep neural networks},
  author={Huang, Wei and Xiao, Liang and Wei, Zhihui and Liu, Hongyi and Tang, Songze},
  journal={IEEE Geoscience and Remote Sensing Letters},
  volume={12},
  number={5},
  pages={1037--1041},
  year={2015},
  publisher={IEEE}
}
```