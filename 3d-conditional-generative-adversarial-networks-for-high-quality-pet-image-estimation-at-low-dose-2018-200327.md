### Paper:

3D conditional generative adversarial networks for high-quality PET image estimation at low dose

### Author:

Yan Wang, Biting Yu, Lei Wang, Chen Zu, David S. Lalush, Weili Lin, Xi Wu, Jiliu Zhou, Dinggang Shen, and Luping Zhou

### Year:

2018

### Notes:

泛读。这篇文章研究的是 PET 重建，使用的方法是 condition GAN，因此可以归到 CNN 直接重建这一类方法。可以认为损失函数是图像域的损失函数加上对抗loss。这里的图像域 loss 是 1 范数。 生成器网络结构是 3D U-net with skip connection，网络输入是 low-dose 的重建，并且是基于 patch。另外作者提出多个 GAN refine 的重建方式。refine 指的是 GAN 的输出作为下一个 GAN 的输入，训练的时候是逐个 GAN 训练。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200327203058.png"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200327203117.png"/>

### Bibtex:

```latex
@article{wang20183d,
  title={3D conditional generative adversarial networks for high-quality PET image estimation at low dose},
  author={Wang, Yan and Yu, Biting and Wang, Lei and Zu, Chen and Lalush, David S and Lin, Weili and Wu, Xi and Zhou, Jiliu and Shen, Dinggang and Zhou, Luping},
  journal={NeuroImage},
  volume={174},
  pages={550--562},
  year={2018},
  publisher={Elsevier}
}
```

### 其他

