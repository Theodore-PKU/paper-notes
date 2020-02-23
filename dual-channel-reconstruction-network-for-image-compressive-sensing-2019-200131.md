### Paper:



### Author:

Dual-Channel Reconstruction Network for Image Compressive Sensing

### Year:

2019

### Notes:

阅读时间：2020.01.31

泛读。这篇文章研究的是 CS 重建，作者用的方法其实很简单，即使用双分支的网络，一个分支用的 residual block，一个网络用的是 dense block。这篇文章的 CS 重建基于 patch，考虑两种情形，第一种是给定的随机高斯矩阵，第二种是用全连接层作为测量矩阵（仍然是矩阵运算），因为是 block CS，所以最后用了 BM3D 来去除 block artifects。从实验的效果看，没有觉得特别好，甚至比不过其他方法。太过简单，没有参考价值。

网络示意图如下（两个 residual block，一个 dense block）：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-31%E4%B8%8B%E5%8D%8810.01.26.png"/>

residual block 和 dense block 的结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-31%E4%B8%8B%E5%8D%8810.01.44.png"/>

### Bibtex:

```latex
@article{zhang2019dual,
  title={Dual-Channel Reconstruction Network for Image Compressive Sensing},
  author={Zhang, Zhongqiang and Gao, Dahua and Xie, Xuemei and Shi, Guangming},
  journal={Sensors},
  volume={19},
  number={11},
  pages={2549},
  year={2019},
  publisher={Multidisciplinary Digital Publishing Institute}
}
```

