### Paper:

Deep Residual Learning for Accelerated MRI using Magnitude and Phase Networks

### Author:

Dongwook Lee, Jaejun Yoo, Sungho Tak and Jong Chul Ye

### Year:

2018

### Notes:

阅读日期：2020.01.16

泛读。这篇文章和之前作者写过的一篇ISBI 的文章很像。不过加了一些新东西。这篇文章的研究的是 MRI 的重建。使用的方法是 U-Net 结构的残差学习。作者把重建网络分成两个独立的网络，一个是 magnitude 网络，一个是 phase 网络。如果只有 magnitude 网络也可以训练，但是就仅仅是图像域上的处理。如果有 phase 网络（当 kspace 数据可用时），可以和 magnitude 网络合起来，并且可以进行 recursive 的迭代网络。

网络结构比较特别的地方在于多了一个橙色部分的 skip-connection，相当于 residual learning。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-16%E4%B8%8B%E5%8D%889.29.42.png"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-16.png" width="60%"/>



迭代网络算法

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-16%E4%B8%8B%E5%8D%889.52..png" width="50%"/>

### Bibtex:

```latex
@article{lee2018deep,
  title={Deep residual learning for accelerated MRI using magnitude and phase networks},
  author={Lee, Dongwook and Yoo, Jaejun and Tak, Sungho and Ye, Jong Chul},
  journal={IEEE Transactions on Biomedical Engineering},
  volume={65},
  number={9},
  pages={1985--1995},
  year={2018},
  publisher={IEEE}
}
```

