### Paper:

Fast and accurate reconstruction of human lung gas MRI with deep learning

### Author:

Caohui Duan, Xianping Sun

He Deng, Lin Ma, Sa Xiao, Xin Lou, Junshuai Xie, Chaohui Ye, Haidong Li, Xin Zhou

### Year:

2019

### Notes:

阅读日期：2020.03.02

泛读。这篇文章研究的是 MRI 的重建，有一些特殊，是 HP gas mri，不过模型仍然不变。作者使用的方法是深度学习，由两个网络组成，第一个网络 C-net 的输入是 zero-fill 图像，输出是第二个网络 F-net 的输入，最终重建应该是 F-net 的输出。C-net 的输出损失函数是 L-2 范数，而 F-net 的损失函数还包括一个 proton prior knowledge。这个 knowledge 其实是 lung 的分割结果，也就是将损失函数限制在这个分割结果当中。不过文章中写的第二个损失函数由包括了第一个网络的参数，不知道是先训练一个网络，还是两个网络两个损失函数同时训练。两个网络结构都是 U-net，没有什么特别的地方。

两个损失函数

<img src="http://latex.codecogs.com/svg.latex? \underset{\mathbf{x}_{\mathbf{C}}}{\operatorname{argmin}}\left\|\mathbf{x}-\mathbf{x}_{C}\right\|_{2}^{2}=\underset{\boldsymbol{\theta}_{\mathcal{C}}}{\operatorname{argmin}}\left\|\mathbf{x}-C\left(\mathbf{x}_{0} ; \boldsymbol{\theta}_{C}\right)\right\|_{2}^{2}">

<img src="http://latex.codecogs.com/svg.latex? \underset{\boldsymbol{\theta}_{c}, \boldsymbol{\theta}_{F}}{\operatorname{argmin}}\left\|\left(\mathbf{x}-F\left(C\left(\mathbf{x}_{0} ; \boldsymbol{\theta}_{C}\right) ; \boldsymbol{\theta}_{F}\right)\right) \cdot \mathbf{m}\right\|_{2}^{2}">

m 和分割的 lung 区域有关。

<img src="https://cdn.mathpix.com/snip/images/CRlYYGQyWU1A3nNVF_FNGA3fg0GyakMsNWBHCqhihyo.original.fullsize.png" width="80%"/>

 <img src="https://cdn.mathpix.com/snip/images/pP42zEubzbN-cWwaTJU4FHDQCx5Dhvq6LR4oDq8IAaQ.original.fullsize.png" />

### Bibtex:

```latex
@article{duan2019fast,
  title={Fast and accurate reconstruction of human lung gas MRI with deep learning},
  author={Duan, Caohui and Deng, He and Xiao, Sa and Xie, Junshuai and Li, Haidong and Sun, Xianping and Ma, Lin and Lou, Xin and Ye, Chaohui and Zhou, Xin},
  journal={Magnetic resonance in medicine},
  volume={82},
  number={6},
  pages={2273--2285},
  year={2019},
  publisher={Wiley Online Library}
}
```

### Related work

none

### Code or link

none