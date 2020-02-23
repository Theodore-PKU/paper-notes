### Paper:

Adversarial and Perceptual Reﬁnement for Compressed Sensing MRI Reconstruction

### Author:

Maximilian Seitzer, Guang Yang, Jo Schlemper, Ozan Oktay, Tobias Würﬂ, Vincent Christlein, Tom Wong, Raad Mohiaddin, David Firmin, Jennifer Keegan, Daniel Rueckert, and Andreas Maier

### Year:

2018

### Notes:

阅读日期：2020.01.17

精读。这篇文章研究的是在MRI 压缩感知重建中损失函数的应用。使用深度学习的MRI 重建方法中，最常使用的是 MSE loss，也有使用 perceptual loss 和对抗loss，作者认为直接将这些损失函数放一起训练会使得PSNR变差。作者提出的方法是两阶段的重建过程。第一个阶段使用重建网络（cascade 网络），损失函数是 MSE，第二个阶段是 refine 网络（Unet），使用的损失函数是对抗loss和 perceptual loss。

整体的框架如下图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-17%E4%B8%8B%E5%8D%8810.25.16.png"/>

这篇文章作者不关注使用的网络结构，而是如何运用这些loss。下面是更具体的一些细节。

训练过程：分为两个阶段训练，第一个阶段训练完，重建网络的参数固定。值得说明的是，refine 网络的输出和重建网络的输出的线性和（$\lambda$ 控制比例，是可学习的参数，初始化为 0，表示网络还没训练，仅仅靠重建网络的结果）是最终的 refine 的结果。

损失函数：实际上，作者还增加了两个loss，一个是 refine 网络的输出的 1 范数loss（作者说目的是让 refine 网络的变动不要太多），一个判别网络中的特征loss（即让重建的结果的特征和groundtruth 的特征相似）

评价指标：除了 PSNR，专家的评判，作者增加了一个指标 sis，其实就是将重建的图像输入到一个训练好的分割网络中，看分割的效果（dice）。但是这个要求分割网络可以训练。

实验的结果：表明作者提出的方法，虽然 psnr 值比不上 cascade 网络，但是专家的评分和 sis 的值都高，所以视觉的重建效果更好。

### Bibtex:

```latex
@inproceedings{seitzer2018adversarial,
  title={Adversarial and perceptual refinement for compressed sensing MRI reconstruction},
  author={Seitzer, Maximilian and Yang, Guang and Schlemper, Jo and Oktay, Ozan and W{\"u}rfl, Tobias and Christlein, Vincent and Wong, Tom and Mohiaddin, Raad and Firmin, David and Keegan, Jennifer and others},
  booktitle={International Conference on Medical Image Computing and Computer-Assisted Intervention},
  pages={232--240},
  year={2018},
  organization={Springer}
}
```

