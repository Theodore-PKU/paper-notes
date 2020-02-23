### Paper:

LAPRAN: A Scalable Laplacian Pyramid Reconstructive Adversarial Network for Flexible Compressive Sensing Reconstruction

### Author:

Kai XU, Zhikang Zhang, Fengbo Ren

### Year:

2018

### Notes:

阅读日期：2020.01.11

精读。这篇文章考虑的是一般的图像压缩感知问题。作者的创新点在于从图像金字塔的角度出发来重建图像。作者在文章中表示，一般的方法，不论是传统的基于模型，还是数据驱动的方法，都是重建固定的分辨率，一旦测量值的数量不够，就无法生成质量较好的图像。文中提出的方法则非常灵活，整体的想法是先用较少的测量值生成一个分辨率较低的图像，然后使用更多的测量值生成分辨率更高的图像。如此一来，即使测量值较少，对于生成较高分辨率的图像有困难时，也可以生成分辨率较低的图像（这样用上采样也可以得到还可以的图像，不至于直接崩坏）。由于是逐层生成不同分辨率的图像，每次生成图像如何利用测量值，之前生成的较低分辨率的图像如何利用是需要考虑的。

作者的具体做法如下。

首先，整体框架是一个 cascade 框架，每一个层次具有一定独立性，重建的图像构成图像金字塔。每个层次的网络设计是一个GAN，每个GAN的输入是上一层生成的图像和测量值。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-11%E4%B8%8B%E5%8D%882.27.23.png"/>

测量值：获取不同测量值的方式是在同一个sensing 矩阵中，选择不同数量的行。对于低分辨率的部分，使用较少的测量值，对于较高分辨率的部分，使用较高的测量值。相邻层级测量值数量的比例是 2，作者说最高是4。总的测量数量其实没有增加，因为较低分辨率只是使用了测量值的子集（由此我的问题是，为什么每个层级不用相同的完整的测量值？）

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-11%E4%B8%8B%E5%8D%882.27.41.png" width="50%"/>

每一个 RAN 的结构如下：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-11%E4%B8%8B%E5%8D%882.32.49.png"/>

分析：i2 表示的上一层的输出，通过编码器得到信息 c1（特征抽取部分），和测量值 y2 相同的长度（和侧测量值融合），u2 是 i2 的上采样（可以看成是SR，作为该层生成图像的低频部分），c1 和 y2 再生成残差部分 r2，和 u2 合并为重建结果 o2。

损失函数是对抗loss和图像域的loss，每个层级都有。训练的时候先训练较低分辨率的部分，然后将参数迁移到较高分辨率。

从实验结果上看，作者提出的这种方法效果很好。

> 作者提出的这种方式相当于先重建低频再重建高频，在已知低频部分的情况下，搜索高频部分时，有减小搜索空间的效果（这个说法很有启发性）。感觉多尺度或者图像金字塔这一点和图像概率分布是有很大的关系。不过需要指出的是，在这篇文章中，没有看到 data fidelity。而且对于图像金字塔，data fidelity 似乎没那么容易考虑。
>
> 不知道为什么引用的人那么少。

### Bibtex:

```latex
@inproceedings{xu2018lapran,
  title={Lapran: A scalable laplacian pyramid reconstructive adversarial network for flexible compressive sensing reconstruction},
  author={Xu, Kai and Zhang, Zhikang and Ren, Fengbo},
  booktitle={Proceedings of the European Conference on Computer Vision (ECCV)},
  pages={485--500},
  year={2018}
}
```

