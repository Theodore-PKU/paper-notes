### Paper:

Conditional WGANs with Adaptive Gradient Balancing for Sparse MRI Reconstruction

### Author:

Itzik Malkiel, Sangtae Ahn, Valentina Taviani, Anne Menini, Lior Wolf, and Christopher J. Hardy

### Year:

2019

### Notes:

阅读日期：2020.03.02

泛读。这篇文章研究的是 MRI 的重建，作者使用的方法是 WGAN，生成器是类似 unroll 的迭代形式的网络结构，每个 iteration 包含 DC 和 CNN 的部分，每个 iteration 之间有 dense connection。损失函数则是对抗loss + MSE。生成器的输入是 zero-fill 重建，所以是 conditional GAN。此外作者对损失函数中的两个部分如何平衡，使用了一个 balance 的技巧，主要的思想就是如果 WGAN 的梯度（关于生成图像的梯度，不是网络参数的梯度）的 std 比 MSE 的梯度大，超出一定比例了，那么就增加 beta，减少 WGAN 的影响。判别器用的是 patchGAN 的判别器，而且判别器的输入也是 conditional，包括 zero-fill 的重建。

整体的结构

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200302204534.png"/> <img src="https://cdn.mathpix.com/snip/images/gmb1-FNT84N3w4CE1MDVDWzBdLcrO4GJh1tlPyVfCxc.original.fullsize.png" />

<img src="https://cdn.mathpix.com/snip/images/RFuxx3d66eCYNVbqf46I3cwGwPkZDwNyr7YOIDAW_yM.original.fullsize.png" />

### Bibtex:

```latex
@article{malkiel2019conditional,
  title={Conditional WGANs with Adaptive Gradient Balancing for Sparse MRI Reconstruction},
  author={Malkiel, Itzik and Ahn, Sangtae and Taviani, Valentina and Menini, Anne and Wolf, Lior and Hardy, Christopher J},
  journal={arXiv preprint arXiv:1905.00985},
  year={2019}
}
```

### Related work

none

### Code or link

none