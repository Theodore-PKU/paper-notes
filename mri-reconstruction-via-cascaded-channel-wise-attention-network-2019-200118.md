### Paper:

MRI Reconstruction via Cascaded Channel-wise Attention Network

### Author:

Qiaoying Huang, Dong Yang, Pengxiang Wu, Hui Qu, Jingru Yi, Dimitris Metaxas

### Year:

2019

### Notes:

阅读日期：2020.01.18

泛读。这篇文章研究的是 MRI 重建。作者的创新点在于增加了 attention 层，以及使用了一个 long skip connection。整体框架使用的是 cascade 的网络结构，有两个主要的模块，一个是 UAC，即包含 attention 层（针对 decoder 部分的每个level的输出特征图）的 U-Net，另一个是 data fidelity（这个和 cascade 那篇最早的文章基本一样）。

网络结构的示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-18%E4%B8%8B%E5%8D%881.06.35.png"/>

UAC 中的 attention 层的计算过程（在 Unet 的decoder部分，每一level 的输出都都经过一个 attention 的计算）：

作者提出的 UCA 模块，。计算过程为：

1.  计算每个通道的feature map 的均值,得到一个向量 z，

   <img src="http://latex.codecogs.com/svg.latex? z_{c}=\frac{1}{H \times W} \sum_{i=1}^{H} \sum_{j=1}^{W} f_{c}[i, j] (c=1, \ldots, C)" border="0"/>

2. 对z做一个变换（卷积+ReLU+卷积+Sigmoid，见示意图的右上角），然后乘上原来的feature map.

   <img src="http://latex.codecogs.com/svg.latex? \hat{x}_{c}=\sigma\left(\delta\left(z * \mathbf{W}_{1}\right) * \mathbf{W}_{2}\right) \odot f_{c}" border="0"/>

long skip connection 指的是把最后一个 UCA 模块本身的残差计算替换成 long skip connection。损失函数是图像域的 1 范数和 perceptual loss，其中 perceptual loss 使用了多层的特征图：

<img src="http://latex.codecogs.com/svg.latex? l_{p}^{\theta}\left(x, x_{s}\right)=\sum_{k=1}\left\|\phi_{V G G}^{k}(x)-\phi_{V G G}^{k}\left(x_{s}\right)\right\|_{2}^{2}\)" border="0"/>

### Bibtex:

```latex
@inproceedings{huang2019mri,
  title={MRI reconstruction via cascaded channel-wise attention network},
  author={Huang, Qiaoying and Yang, Dong and Wu, Pengxiang and Qu, Hui and Yi, Jingru and Metaxas, Dimitris},
  booktitle={2019 IEEE 16th International Symposium on Biomedical Imaging (ISBI 2019)},
  pages={1622--1626},
  year={2019},
  organization={IEEE}
}
```

