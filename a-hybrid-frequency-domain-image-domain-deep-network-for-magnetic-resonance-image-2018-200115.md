### Paper:

A Hybrid Frequency-domain/Image-domain Deep Network for Magnetic Resonance Image Reconstruction

### Author:

Roberto Souza, and Richard Frayne

### Year:

2018（2019年发出来了）

### Notes:

阅读日期：2020.01.15

泛读。这篇文章研究的是MRI重建。作者的想法是将 kspace 域上的重建也考虑进来。作者发现之前的工作都是在图像域上处理，kspace 最多是用于 data fidelity 的约束。作者的框架也比较简单，用一个 residual U-Net 来重建 kspace，一个 U-net 来重建图像，两个网络用 IDFT 连接。kspace 用 2 通道表示复数，图像域上用 实数来处理，所以 IDFT 有一个模长运算。整体上可以把 kspace 网络当作是初步处理，图像网络当作是进一步重建。两个网络是串行的。

网络结构：（注意，作者多次使用了 normalization，形如<img src="http://latex.codecogs.com/svg.latex? F_{u_{n o r m}}=\frac{F_{u}-\mu_{F_{u_{t r a i n}}}}{\sigma_{F_{u_{t r a i n}}}}" border="0"/>，标准差和均值都是用训练集的数据，而不是一张图像的，也有对应的 Inv normalization）

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-15%E4%B8%8A%E5%8D%8811.12.27.png"/>

整体的框架：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-15%E4%B8%8A%E5%8D%8811.12.19.png"/>

损失函数是两个网络的 NRMSE：

<img src="http://latex.codecogs.com/svg.latex? N R M S E(\widehat{f}, f)=\frac{\sqrt{\frac{1}{M} \sum_{i=1}^{M}[\widehat{f}(i)-f(i)]^{2}}}{\max (f)-\min (f)}" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \mathcal{L}=\frac{1}{N} \sum_{i=1}^{N} w_{1} \times N R M S E\left(F_{i}, \widehat{F}_{i}\right)+w_{2} \times N R M S E\left(f_{i}, \widehat{f}_{i}\right)" border="0"/>

实验结果上看，作者提出的方法并不是最好的（最好的是 deep cascade），但是在作者给出的图中，细节部分，这种方法表现得看起来更好，有点去噪的效果，groudtruth 带一点噪声，所以指标上不如 deep cascade 吧。实验结果：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-15%E4%B8%8A%E5%8D%8811.20.19.png"/>

### Bibtex:

```latex
@inproceedings{souza2019hybrid,
  title={A hybrid frequency-domain/image-domain deep network for magnetic resonance image reconstruction},
  author={Souza, Roberto and Frayne, Richard},
  booktitle={2019 32nd SIBGRAPI Conference on Graphics, Patterns and Images (SIBGRAPI)},
  pages={257--264},
  year={2019},
  organization={IEEE}
}
```

