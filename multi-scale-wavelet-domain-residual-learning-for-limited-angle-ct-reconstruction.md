### Paper:

Multi-Scale Wavelet Domain Residual Learning for Limited-Angle CT Reconstruction

### Author:

Jawook Gu, Jong Chul Ye

### Year:

2017

### Notes:

这篇文章解决的问题是有限角的 CT 重建。作者的思路从三个角度入手：

1. CT 伪影的相似性、低维特点。因此采用生成残差的方式。
2. 方向性，伪影具有方向的特点。因此在小波域上进行处理（输入和输出都是小波域）。
3. 全局性，伪影的特征不仅仅是局部的，有 global 的特征。所以使用具有多分辨率的网络架构 Unet with skip.

细节：作者使用了 countour transform，展开三次，把所有的小波都作为输入，一共15个channel。作者比较了重建图像域而且不使用多分辨率的信息、重建图像域且使用多分辨率信息两种网络，作者在文中提出的小波残差学习网络效果最好。

网络结构示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-26%E4%B8%8B%E5%8D%887.00.13.png" style="zoom:50%;" />

### Bibtex:

```
@article{gu2017multi,
  title={Multi-scale wavelet domain residual learning for limited-angle CT reconstruction},
  author={Gu, Jawook and Ye, Jong Chul},
  journal={arXiv preprint arXiv:1703.01382},
  year={2017}
}
```