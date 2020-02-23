### Paper:

SEGAN: Structure-Enhanced Generative Adversarial Network for Compressed Sensing MRI Reconstruction

### Author:

Zhongnian Li, Tao Zhang, Peng Wan, Daoqiang Zhang

### Year:

2019

### Notes:

阅读日期：2020.02.22

泛读。这篇文章研究的是 MRI 重建，作者的方法是 GAN，生成网络是 U-net 的改进 SU-net，主要的改进是使用了不同的卷积核大小并行获得特征再融合起来。另一个创新，也是题目中提到的 structure-enhanced，在于损失函数。除了对抗 loss，图像域的 loss，作者增加了两个loss，一个是 ssim loss，一个是关于重建图像和真实图像各自不同的 patch 之间的相似程度（patch pair 的 index 是相同的）要尽量接近。相似程度则是可以灵活自定义。其他就没有什么特别的，我感觉参考价值不大。

<img src="http://latex.codecogs.com/svg.latex? P_{w_{G}}\left(x^{\prime}, G(y)^{\prime}\right=\sum_{i=1}^{N} \sum_{j=i+1}^{N}\left[f\left(x_{i}^{\prime}, x_{j}^{\prime}\right) \left.-f\left(G\left(y^{\prime}\right)_{i}, G\left(y^{\prime}\right)_{j}\right)\right]^{2} " border="0"/>

<img src="https://cdn.mathpix.com/snip/images/_5jhFa53c5LF3qrMPNLpdg6Aya_zynKOLILj3BGR1Qc.original.fullsize.png" />

### Bibtex:

```latex
@inproceedings{li2019segan,
  title={SEGAN: structure-enhanced generative adversarial network for compressed sensing MRI reconstruction},
  author={Li, Zhongnian and Zhang, Tao and Wan, Peng and Zhang, Daoqiang},
  booktitle={Proceedings of the AAAI Conference on Artificial Intelligence},
  volume={33},
  pages={1012--1019},
  year={2019}
}
```

