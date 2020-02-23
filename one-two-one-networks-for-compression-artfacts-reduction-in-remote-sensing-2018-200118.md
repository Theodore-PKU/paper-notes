### Paper:

One-Two-One Networks for Compression Artifacts Reduction in Remote Sensing

### Author:

Baochang Zhang, Jiaxin Gu, Chen Chen, Jungong Han, Xiangbo Su, Xianbin Cao, Jianzhuang Liu

### Year:

2018

### Notes:

阅读日期：2020.01.18

泛读。这篇文章研究的是 Compression artifacts reduction (CAR)，类似一个去噪问题。主题和压缩感知虽不相关，但是其设计的网络结构值得借鉴。作者表示，网络结构受到图像金字塔的启发，将图像的复原问题分解为高频和低频两个部分。甚至，还可以进一步推广为 multi-level 的形式。具体的网络结构见下图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-18%E4%B8%8B%E5%8D%889.12.41.png"/>

其中 subnetwork 可以有多种形式（res-net，dense-net，或者是普通的cnn）：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-18%E4%B8%8B%E5%8D%889.14.01.png"/>

网络结构的分析：首先经过一个卷积层，然后分成两支，一支进行下采样后再接 sub-network，然后再上采样回来，另一支则直接接 sub-network，这时，两支相加得到的是低频部分，相减得到高频部分，再分别接多个卷积层，最后 diffence model 乘上一个系数和 summation model 相加，最后再接一些 resUnit。最后是一个残差结构和最开始的输入相加。可以发现相减的操作和图像金字塔非常类似。

另外，如果不只是划分为低频高频，可以像小波那样一层一层地处理，在下图中则将1/2 scale进一步分为了 1/4 scale，构成一个嵌套或者递归的形式。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-18%E4%B8%8B%E5%8D%889.15.45.png"/>

> 感觉图像恢复等其他任务的网络结构对于压缩感知重建也是有借鉴意义。尤其是当我们试图用深度学习来刻画图像的流形，如果结合 multi-level sparsity 等想法，低频高频的划分是非常重要的。

### Bibtex:

```latex
@article{zhang2018one,
  title={One-two-one networks for compression artifacts reduction in remote sensing},
  author={Zhang, Baochang and Gu, Jiaxin and Chen, Chen and Han, Jungong and Su, Xiangbo and Cao, Xianbin and Liu, Jianzhuang},
  journal={ISPRS journal of photogrammetry and remote sensing},
  volume={145},
  pages={184--196},
  year={2018},
  publisher={Elsevier}
}
```

