### Paper:

Complex Fully Convolutional Neural Networks for MR Image Reconstruction

### Author:

Muneer Ahmad Dedmari, Sailesh Conjeti, Santiago Estrada, Phillip Ehses, Tony Stöcker, and Martin Reuter

### Year:

2018

### Notes:

阅读日期：2020.02.29

泛读。这篇文章研究的是MRI重建，用的方法是类似 U-Net 的网络结构，并且在最后一层有一个 DC 的操作。这篇的主要创新点在对复数的处理。一般的做法仅仅是双通道表示，并不是严格按照复数的运算来处理。这篇文章虽然也是将实部和虚部分别用不同的通道表示，但是具体的运算严格遵守了复数的处理。损失函数用的是图像域的 L2范数和 SSIM loss。网络结构的block部分用的是 dense connection的结构。

复数的运算表示：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{W} *_{\mathbb{C}} \mathbf{h}=\left(\mathbf{a} * \mathbf{W}_{\mathbf{R}}-\mathbf{b} * \mathbf{W}_{\mathbf{I}}\right)+i\left(\mathbf{a} * \mathbf{W}_{\mathbf{I}}+\mathbf{b} * \mathbf{W}_{\mathbf{R}}\right)" border="0"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200301113808.png"/>

DC是保持 kspace 的采样部分不变，这相当于假设没有误差。

### Bibtex:

```latex
@inproceedings{dedmari2018complex,
  title={Complex fully convolutional neural networks for mr image reconstruction},
  author={Dedmari, Muneer Ahmad and Conjeti, Sailesh and Estrada, Santiago and Ehses, Phillip and St{\"o}cker, Tony and Reuter, Martin},
  booktitle={International Workshop on Machine Learning for Medical Image Reconstruction},
  pages={30--38},
  year={2018},
  organization={Springer}
}
```

