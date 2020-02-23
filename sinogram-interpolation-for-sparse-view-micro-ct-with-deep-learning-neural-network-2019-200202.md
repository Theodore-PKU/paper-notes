### Paper:

Sinogram interpolation for sparse-view micro-CT with deep learning neural network

### Author:

Xu Dong , Swapnil Vekhande, Guohua Cao

### Year:

2019

### Notes:

阅读时间：2020.02.02

泛读。这篇文章研究的是 sparse-view CT 的重建，使用的方法是先对 sinogram 进行插值，然后用一个 U-Net 得到插值更准确的 sinogram，最后用 FBP 算法得到重建结果。作者使用初步差值后的 sinogram 的 patch 训练，损失函数是 sinogram 的 L-2 范数。U-Net 的上采样和下采样都是卷积层，还有 residual 的结构。其他没有什么特别有意思的东西，参考意义不大。

整体的流程：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-02%E4%B8%8B%E5%8D%888.29.56.png"/>

网络结构示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-02%E4%B8%8B%E5%8D%888.40.47.png"/>

### Bibtex:

```latex
@inproceedings{dong2019sinogram,
  title={Sinogram interpolation for sparse-view micro-CT with deep learning neural network},
  author={Dong, Xu and Vekhande, Swapnil and Cao, Guohua},
  booktitle={Medical Imaging 2019: Physics of Medical Imaging},
  volume={10948},
  pages={109482O},
  year={2019},
  organization={International Society for Optics and Photonics}
}
```

