### Paper:

Deep Residual Learning for Compressed Sensing MRI

### Author:

Dongwook Lee, Jaejun Yoo and Jong Chul Ye

### Year:

2017

### Notes:

这篇文章的主要想法和作者的另一篇 [Deep artifact learning for compressed sensing and parallel MRI](deep-artifact-learning-for-compressed-sensing-and-parallel-mri.md) 是一样，应该是同一个内容的不同会议的投稿。想法就是残差的拓扑结构更简单，所用深度卷积网络学残差，网络用的是Unet。因为mri数据是复数形式，所以作者考虑用两个网络来表示，一个是角度，一个是幅值。

### Bibtex:

```latex
@inproceedings{lee2017deep,
  title={Deep residual learning for compressed sensing MRI},
  author={Lee, Dongwook and Yoo, Jaejun and Ye, Jong Chul},
  booktitle={2017 IEEE 14th International Symposium on Biomedical Imaging (ISBI 2017)},
  pages={15--18},
  year={2017},
  organization={IEEE}
}
```