### Paper:

Learning from our neighbours: a novel approach on sinogram completion using bin-sharing and deep learning to reconstruct high quality 4DCBCT

### Author:

Joel Beaudry and Pedro L. Esquinas

### Year:

2019

### Notes:

阅读时间：2020.02.06

泛读。这篇文章研究的是 CT 的重建。没啥可以参考的。大致的做法是先用插值得到对 sinogram 的基本估计，然后用 CNN 得到 high-quality 的估计，最后用传统的重建算法计算。网络结构用的是 U-net，损失函数方面作者用了 MAE、MS-SSIM 和两者的结合来比较。

下图从左到右分别是采样的 sinogram，插值后的 sinogram，重建后的 sinogram 和 groundtruth。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-06%E4%B8%8B%E5%8D%888.18.58.png"/>

网络结构方面，作者用了 residual learning 的 U-Net。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-06%E4%B8%8B%E5%8D%888.19.0.png"/>

### Bibtex:

```latex
@inproceedings{beaudry2019learning,
  title={Learning from our neighbours: a novel approach on sinogram completion using bin-sharing and deep learning to reconstruct high quality 4DCBCT},
  author={Beaudry, Joel and Esquinas, Pedro L and Shieh, Chun-Chien},
  booktitle={Medical Imaging 2019: Physics of Medical Imaging},
  volume={10948},
  pages={1094847},
  year={2019},
  organization={International Society for Optics and Photonics}
}
```

