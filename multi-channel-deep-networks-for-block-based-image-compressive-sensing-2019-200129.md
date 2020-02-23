### Paper:

Multi-Channel Deep Networks for Block-Based Image Compressive Sensing

### Author:

Siwang Zhou, Yan He, Yonghe Liu, and Chengqing Li

### Year:

2019

### Notes:

阅读时间：2020.01.29

泛读。这一篇文章研究的是 BCS 的重建。作者的想法是不同的 patch 用不同的采样率，采样率直接用 salient value 来分配。但是这样会有很多不同的采样率，实际上作者用的是固定的一些采样率，只是根据 salient value 来确定使用哪一个相近的采样率。每一个采样率对应了一个卷积形式的采样层，属于同一个采样率的patch 都使用相同的采样层采样。对应的，每个采样层有 1x1 的卷积初步重建层，作为重建的初始结果。此后，所有的patch合起来进行进一步 enhance，使用一个结构为 unroll 的 BCS 算法的网络。这个 BCS 算法是 patch 和 D-AMP 算法的结合。在梯度步，使用 patch 形式的计算（因为每个patch 的采样都有可能不同，需要用不同的采样卷积层来计算），在去噪步，对整幅图像使用 DnCNN 网络（5层），两步迭代进行。训练时，使用常见的两步训练法，第一步时训练初步重建层的结果，第二步时训练最终重建的结果。

整体的结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-29%E4%B8%8B%E5%8D%8810.03.13.png" width="70%"/>

采样部分的结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-29%E4%B8%8B%E5%8D%8810.06.40.png" width="60%"/>

Deep reconstruction 部分的示意图：左边是梯度步的计算，$\Phi^*_{B,j}$ 是可学习的卷积层，$\Phi_{B,j}$ 和采样层一样

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-29%E4%B8%8B%E5%8D%8810.04.48.png"/>

梯度步的计算公式：

<img src="http://latex.codecogs.com/svg.latex? r_{i}^{t+1}=\hat{x}_{i}^{t}+\mathbf{\Phi}_{\mathrm{B}, \mathrm{j}}^{*}\left(y_{i}-\mathbf{\Phi}_{B, j} \hat{x}_{i}^{t}\right)" border="0"/>

### Bibtex:

```latex
@article{zhou2019multi,
  title={Multi-Channel Deep Networks for Block-Based Image Compressive Sensing},
  author={Zhou, Siwang and He, Yan and Liu, Yonghe and Li, Chengqing},
  journal={arXiv preprint arXiv:1908.11221},
  year={2019}
}
```