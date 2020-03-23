### Paper:

Conditional generative adversarial network for 3D rigid‐body motion correction in MRI

### Author:

Patricia M. Johnson, Maria Drangova

### Year:

2019

### Notes:

泛读。这篇文章研究的是 MRI 中的 motion correction。motion 在 MRI 成像中是常见的问题，这篇文章的主要做法是先模拟生成受到 motion 影响的图像，然后用 condition GAN 生成 correction 的结果。因此输入是 corrupted image，输出是 correction image。整个训练网络就是一个 cGAN，生成器类似于 U-net 结构。个人感觉这篇文章也就模拟生成部分有点用，其他没什么参考价值。模拟生成的方式是：ground-truth 进行带参数的 motion 变换得到多张有一点 motion 的不同图像——对这些图像计算 kspace，每个 kspace 都只取一部分——将 kspace 拼凑起来——对 kspace 做傅立叶逆变换得到 corrupted image。这篇文章的研究对象是 3D。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200323162255.png"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200323162312.png"/>

### Bibtex:

```latex
@article{johnson2019conditional,
  title={Conditional generative adversarial network for 3D rigid-body motion correction in MRI},
  author={Johnson, Patricia M and Drangova, Maria},
  journal={Magnetic resonance in medicine},
  volume={82},
  number={3},
  pages={901--910},
  year={2019},
  publisher={Wiley Online Library}
}
```

### 其他

