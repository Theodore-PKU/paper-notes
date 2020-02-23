### Paper:

FR-Net: Joint Reconstruction and Segmentation in Compressed Sensing Cardiac MRI

### Author:

Qiaoying Huang, Dong Yang, Jingru Yi, Leon Axel, and Dimitris Metaxas

### Year:

2019

### Notes:

阅读日期：2020.02.20

泛读。这篇文章研究的是 MRI 的重建，使用的方法是和一个分割任务联合训练。总体来说，网络结构由两个网络构成，第一个是基于 FISTA 算法的重建网络，第二个是 U-net 负责分割。两个网络以 cascade 的形式串联。损失函数就是重建 loss 和 分割 loss 之和。关于 FISTA 算法的展开，作者和一般的方法有一些区别，proximal 算子部分作者用一个一阶梯度算法来计算的，所以展开的时候局部是一个迭代算法，作者用卷积网络和全连接层替代的是这个一阶梯度迭代过程的一些计算。

原始目标函数：

<img src="http://latex.codecogs.com/svg.latex? x^{*}=\arg \min _{x} \frac{1}{2}\|E(F(x))-y\|_{2}^{2}+\lambda g(x)" border="0"/>

FISTA 算法的迭代：

<img src="http://latex.codecogs.com/svg.latex? x_{k}=\arg \min _{x}\left\{\lambda g(x)+\frac{L_{k}}{2}\left\|x-\left(v_{k}-\frac{1}{L_{k}} \nabla f\left(v_{k}\right)\right)\right\|^{2}\right\}" border="0"/>

考虑 g 的一般性，使用一阶梯度迭代来计算 $x_k$:

<img src="http://latex.codecogs.com/svg.latex? z_{k, n}=z_{k, n-1}-\mu\left\{\lambda \nabla g\left(z_{k, n-1}\right)+L_{k}\left(z_{k, n-1}-v_{k}\right)+F^{T}\left(E\left(F\left(v_{k}\right)\right)-y\right)\right\}" border="0"/>

作者把花括号里的第一项替换为卷积网络，第二项替换为全连接，第三项不变，得到如下的迭代：

<img src="http://latex.codecogs.com/svg.latex? x_{k}=z_{n-1}-F_{k}\left\{G_{k-1}\left(z_{n-1}\right)+D_{k}\left(z_{n-1}-v_{k}\right)+C_{k}\left(v_{k}\right)\right\}" border="0"/>

网络结构如图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200220162506.png"/>

### Bibtex:

```latex
@inproceedings{huang2019fr,
  title={FR-Net: Joint reconstruction and segmentation in compressed sensing cardiac MRI},
  author={Huang, Qiaoying and Yang, Dong and Yi, Jingru and Axel, Leon and Metaxas, Dimitris},
  booktitle={International Conference on Functional Imaging and Modeling of the Heart},
  pages={352--360},
  year={2019},
  organization={Springer}
}
```

