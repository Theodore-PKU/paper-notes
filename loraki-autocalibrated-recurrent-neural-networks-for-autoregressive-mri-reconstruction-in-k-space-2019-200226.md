### Paper:

LORAKI: Autocalibrated Recurrent Neural Networks for Autoregressive MRI Reconstruction in k-Space

### Author:

Tae Hyung Kim, Pratyush Garg, Justin P. Haldar

### Year:

2019

### Notes:

阅读日期：2020.02.26

泛读。这篇文章研究的是 MRI 的重建，从 kspace 的重建入手。作者的方法结合 RAKI 对 GRAPPA 的改进和 AC-LORAKS 方法。这些方法都是 kspace 重建的。RAKI 概括起来是非线性+卷积，AC-LORAKS 则是利用其他的一些约束比如 low rank 等设计的一个迭代算法。作者结合两者的含义便是在 AC-LORAKS 算法中加入非线性。其他就没有什么特别的地方了。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200226214625.png"/>

### Bibtex:

```latex
@article{kim2019loraki,
  title={LORAKI: Autocalibrated recurrent neural networks for autoregressive MRI reconstruction in k-space},
  author={Kim, Tae Hyung and Garg, Pratyush and Haldar, Jusin P},
  journal={arXiv preprint arXiv:1904.09390},
  year={2019}
}
```

