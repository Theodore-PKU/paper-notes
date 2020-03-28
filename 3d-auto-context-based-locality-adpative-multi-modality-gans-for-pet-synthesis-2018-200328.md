### Paper:

3D auto-context-based locality adaptive multi-modality GANs for PET synthesis

### Author:

Yan Wang, Luping Zhou, Biting Yu, Lei Wang, Chen Zu, David S. Lalush, Weili Lin, Xi Wu, Jiliu Zhou, and Dinggang Shen

### Year:

2019

### Notes:

泛读。这篇文章研究的是 PET 重建，使用的方法是 GAN，属于 CNN 直接投影的类型。和其他很多方法一样，这篇文章也借用其他模态的数据来帮助 PET 的重建，只不过这篇文章的输入是 4 个 image。这篇文章的主要创新是多个模态（3D）的信息融合。传统的做法是直接作为不同的通道输入网络，这篇文章则是先将这四个 image fusion 成一个一个 image，然后作为 GAN 的输入。作者称之为 locality-adaptive，这个部分可以这样解释，把四个输入先分成 N 个不重叠的小区域，相同位置的小区域做加权平均（也就是 1x1x1 卷积），每个位置的卷积都不一样，但是满足 (1) 式（该式对权重有约束）。结果仍是一个小区域的大小，最后再拼起来构成 fusion image。另外，作者还采用了之前他们用过的策略，stack 形式的网络，把 GAN 的输出作为另一个 GAN 的输入进行进一步重建。GAN 的生成网络是 U-net，通过 stride=2 下采样，判别器网络比较一般。这些网络都是 3D 卷积。损失函数是 L1 loss 和 LS 对抗损失函数。基于 patch 训练，目的是增加训练样本，小区域的大小是 8x8x8。

locality adaptive fusion
$$
\begin{aligned}
P_{i}^{C}=& w_{i}^{L} P_{i}^{L}+w_{i}^{T 1} P_{i}^{T 1}+w_{i}^{F A} P_{i}^{F A}+w_{i}^{M D} P_{i}^{M D} \\
& \text { s.t. } w_{i}^{L}+w_{i}^{T 1}+w_{i}^{F A}+w_{i}^{M D}=1 \\
& w_{i}^{L}, w_{i}^{T 1}, w_{i}^{F A}, w_{i}^{M D}>0, i=1, \ldots, N
\end{aligned}
$$
作者提出的创新点有一个问题。因为最后训练的时候是 patch，为什么不同的 patch 在相同的位置的小区域用一样的卷积呢？这不合理。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200328205542.png"/>

### Bibtex:

```latex
@article{wang20183d,
  title={3D auto-context-based locality adaptive multi-modality GANs for PET synthesis},
  author={Wang, Yan and Zhou, Luping and Yu, Biting and Wang, Lei and Zu, Chen and Lalush, David S and Lin, Weili and Wu, Xi and Zhou, Jiliu and Shen, Dinggang},
  journal={IEEE transactions on medical imaging},
  volume={38},
  number={6},
  pages={1328--1339},
  year={2018},
  publisher={IEEE}
}
```

### 其他

Bi L, Kim J, Kumar A, Feng D, and Fulham M, “Synthesis of Positron Emission Tomography (PET) Images via Multi-channel Generative Adversarial Networks (GANs),” in Molecular Imaging, Reconstruction and Analysis of Moving Body Organs, and Stroke Imaging and Treatment: Springer, 2017, pp. 43–51.