### Paper:

Color Image Reconstruction with Perceptual Compressive Sensing

### Author:

Jiang Du, Xuemei Xie, Chenye Wang, and Guangming Shi

### Year:

2018

### Notes:

阅读日期：2020.01.15

 泛读。这篇文章研究的是彩色图像的压缩感知重建。作者的想法是使用 YCbCr 通道来表示，对不同的通道采用不同的感知率。作者认为 luminance 通道的信息最多，所以测量值的数量最多，其余两个通道则比较少。论文中给出的比例是4%、1%、1%，一共是2%。使用的网络结构和作者之前的文章中提出的框架类似。因为对象是彩色图像，作者对三个不同的通道各自用一个神经网络来重建。测量矩阵部分用的是卷积层来学习（网络的第一层），网络的第二层是反卷积，之后接Res-block的神经网络。损失函数用了 perceptual loss。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-15%E4%B8%8B%E5%8D%883.16.40.png"/>

### Bibtex:

```latex
@inproceedings{du2018color,
  title={Color Image Reconstruction with Perceptual Compressive Sensing},
  author={Du, Jiang and Xie, Xuemei and Wang, Chenye and Shi, Guangming},
  booktitle={2018 24th International Conference on Pattern Recognition (ICPR)},
  pages={1512--1517},
  year={2018},
  organization={IEEE}
}
```

