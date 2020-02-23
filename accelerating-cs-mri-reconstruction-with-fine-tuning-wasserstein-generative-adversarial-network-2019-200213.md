### Paper:

Accelerating CS-MRI Reconstruction With Fine-Tuning Wasserstein Generative Adversarial Network

### Author:

MINGFENG JIANG, ZIHAN YUAN, XU YANG, JUCHENG ZHANG, YINGLAN GONG, LING XIA, AND TIEQIANG LI

### Year:

2019

### Notes:

阅读日期：2020.02.13

泛读。这篇文章研究的是 MRI，但是写的很差，关键问题没有说明清楚。简单说，这篇文章的方法就是 WGAN 的思路，损失函数除了对抗 loss，还有 image、kspace 的 mse 和 perceptual loss。generator 的结构是三个 U-net 的 cascade。作者说用了 transfer learning，预训练 U-net 的前两层，但是不知道用的是什么数据？怎么预训练前两层，这些都完全不清楚，所以我觉得写的很不好。WGAN 这个只是在对抗 loss 上的改动，没什么特别创新的地方。没什么参考价值。实验方面，作者试了三种采样方式，三种不同的采样率，评价指标是 PSNR 和 SSIM。

给个网络结构图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200213135746.png" width="60%"/>

### Bibtex:

```latex
@article{jiang2019accelerating,
  title={Accelerating CS-MRI Reconstruction With Fine-Tuning Wasserstein Generative Adversarial Network},
  author={Jiang, Mingfeng and Yuan, Zihan and Yang, Xu and Zhang, Jucheng and Gong, Yinglan and Xia, Ling and Li, Tieqiang},
  journal={IEEE Access},
  volume={7},
  pages={152347--152357},
  year={2019},
  publisher={IEEE}
}
```

