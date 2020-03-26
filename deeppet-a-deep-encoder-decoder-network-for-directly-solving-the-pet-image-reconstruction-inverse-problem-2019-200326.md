### Paper:

DeepPET: A deep encoder–decoder network for directly solving the PET image reconstruction inverse problem

### Author:

Ida Häggström, C. Ross Schmidtlein, Gabriele Campanella, and Thomas J. Fuchs

### Year:

2019

### Notes:

泛读。这篇文章研究的是 PET 重建，方法归类上应该属于 CNN 直接重建，不过输入是 sinogram，而不是其他的初步重建结果。方法本身没有什么特殊的，仅仅是一个 AE 结构的卷积网络。需要指出的地方是，作者在论文中说，为了避免随机噪声的影响，需要对 sinogram 进行预处理。预处理的方法是 PETSTEP。损失函数是 2 范数。

预处理要估计参数：
$$
\hat{g}_{i}=\frac{g_{i}-\gamma_{i}}{\mu_{i}}
$$
$\hat{g}$ 是网络的输入。

给一个网络结构和数据处理过程。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200326230815.png"/>

### Bibtex:

```latex
@article{haggstrom2019deeppet,
  title={DeepPET: A deep encoder--decoder network for directly solving the PET image reconstruction inverse problem},
  author={H{\"a}ggstr{\"o}m, Ida and Schmidtlein, C Ross and Campanella, Gabriele and Fuchs, Thomas J},
  journal={Medical image analysis},
  volume={54},
  pages={253--262},
  year={2019},
  publisher={Elsevier}
}
```

### 其他

Yang B, Ying L, Tang J. Artificial Neural Network Enhanced Bayesian PET Image Reconstruction. IEEE Trans Med Imaging 2018a;37(6):1297–309. 10.1109/TMI.2018.2803681. [PubMed: 29870360]

Wang Y, Yu B, Wang L, Zu C, Lalush DS, Lin W, Wu X, Zhou J, Shen D, Zhou L.3D conditional

generative adversarial networks for high-quality PET image estimation at low dose. Neuroimage 2018;174(October 2017):550–62. 10.1016/j.neuroimage.2018.03.045. [PubMed: 29571715]