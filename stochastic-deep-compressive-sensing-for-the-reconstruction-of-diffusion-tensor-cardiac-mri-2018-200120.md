### Paper:

Stochastic Deep Compressive Sensing for the Reconstruction of Diﬀusion Tensor Cardiac MRI

### Author:

Jo Schlemper, Guang Yang, Pedro Ferreira, Andrew Scot, Laura-Ann McGill, Zohya Khalique, Margarita Gorodezky, Malte Roehl, Jennifer Keegan, Dudley Pennell, David Firmin , and Daniel Rueckert

### Year:

2018

### Notes:

泛读。这篇文章的作者 Schlemper 就是 cascade CNN for MRI 的作者，这篇文章只不过是换了一个成像对象，变成 DT-CMR 而已。整个网络结构和 cascade 是一样。区别的地方在于：1. 除了第一层和最后一层使用 dilated convolution；2. 训练的时候随机丢弃一部分 subnetwork，测试的时候不丢弃；3. 训练时的欠采样程度为 0x～12x，测试时固定欠采样程度。作者分析了使用随机网络结构的优势，但从实验结果上看，很难说有多少提升。

### Bibtex:

```latex
@inproceedings{schlemper2018stochastic,
  title={Stochastic deep compressive sensing for the reconstruction of diffusion tensor cardiac mri},
  author={Schlemper, Jo and Yang, Guang and Ferreira, Pedro and Scott, Andrew and McGill, Laura-Ann and Khalique, Zohya and Gorodezky, Margarita and Roehl, Malte and Keegan, Jennifer and Pennell, Dudley and others},
  booktitle={International Conference on Medical Image Computing and Computer-Assisted Intervention},
  pages={295--303},
  year={2018},
  organization={Springer}
}
```

