### Paper:

Deep Learning Based Framework for Direct Reconstruction of PET Images

### Author:

Zhiyuan Liu, Huai Chen, and Huafeng Liu

### Year:

2019

### Notes:

泛读。这篇文章研究的是 PET 重建，作者并不是考虑 low-dose 重建之类的问题，在论文中仅仅是考虑单纯的重建，当然这个方法完全适用于 low-dose 重建。这篇文章的方法就是直接投影，将 sinogram 投影到重建图像，使用 cGAN 的网络。GAN 的输入是 sinogram，作者在论文中提到了噪声，似乎是 sinogram + noise，输出是图像。生成网络的结构是 U-net。判别网络是 patchGAN 的形式，输入可以是 patch 图像，这样参数量会少一些。另外除了对抗损失函数，还包括 1 范数图像域 loss。

网络结构示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200331193925.png"/>

### Bibtex:

```latex
@inproceedings{liu2019deep,
  title={Deep Learning Based Framework for Direct Reconstruction of PET Images},
  author={Liu, Zhiyuan and Chen, Huai and Liu, Huafeng},
  booktitle={International Conference on Medical Image Computing and Computer-Assisted Intervention},
  pages={48--56},
  year={2019},
  organization={Springer}
}
```

### 其他

