### Paper:

Parameter Constrained Transfer Learning for Low Dose PET Image Denoising

### Author:

Yu Gong, Yueyang Teng, Hongming Shan, Taohui Xiao, Ming Li, Guodong Liang, Ge Wang, and Shanshan Wang,

### Year:

2019

### Notes:

泛读。这篇文章研究的是 PET 重建，使用的方法是 GAN，可以归类到 CNN 直接投影这种类型。这篇文章用了很多技巧，方法的本质是比较简单的。首先，生成网络结构是 U-net 骨架，输入是 3D 的 low-dose PET 图像，输出是 high-dose 级别的图像。网络前四层和后四层是 3D 卷积层，中间是 2D 卷积层（这里不太明白的是 3D 的输出如何作为 2D 卷积层的输入），上采样使用了 deconvolution，使用了 residual learning 的结构。判别器网络比较简单，卷积层 + 全连接层，LeckyReLU 的激活函数。训练方式上，先用 MSE，SSIM 等各种不同的loss组合进行预训练，此时没有对抗损失函数，目的是获得一个比较好的初始网络参数，避免 mode collapse。然后再使用对抗 loss 训练（WGAN），此时还加入了 perceptual loss。perceptual loss 和 VGG 网络相关。训练的策略被作者称为 transfer learning strategy。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200328141253.png"/>

### Bibtex:

```latex
@article{gong2019parameter,
  title={Parameter Constrained Transfer Learning for Low Dose PET Image Denoising},
  author={Gong, Yu and Teng, Yueyang and Shan, Hongming and Xiao, Taohui and Li, Ming and Liang, Guodong and Wang, Ge and Wang, Shanshan},
  journal={arXiv preprint arXiv:1910.06749},
  year={2019}
}
```

### 其他

