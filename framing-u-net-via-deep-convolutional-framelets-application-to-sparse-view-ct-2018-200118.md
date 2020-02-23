### Paper:

Framing U-Net via Deep Convolutional Framelets: Application to Sparse-view CT

### Author:

Yoseob Han and Jong Chul Ye

### Year:

2018

### Notes:

阅读日期：2020.01.18

泛读。这篇文章研究的是稀疏角CT重建。不过整篇文章最主要关注的是如何应用作者提出的 deep convolutional framelet 理论。作者认为一般的 U-Net 不满足该理论中的 frame condition，因此在此基础上提出了改进。为了满足 frame condition，作者在 U-Net 中的 skip concat 之外，还加了一个 bypass connection，这个和 residual structure 非常相似。另外，对于 deep convolutional framelet 中的 $\Phi,\Phi^T$, 作者提出了用 Haar 小波这种 tight frame 作为上下采样的方式（这个在其他文章中见到多次，不过似乎没怎么提 deep convolutional framelet 理论）。

下图的 (a) 是一般的 U-Net，(b) 是改进的 U-Net，(c) 是 tight frame U-Net。需要注意的是，在 (b) 中，residual 的符号是减号而不是加号。关于 deep convolutional framelet，要仔细看那篇理论文章。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-18%E4%B8%8B%E5%8D%884.05.56.png"/>

### Bibtex:

```latex
@article{han2018framing,
  title={Framing U-Net via deep convolutional framelets: Application to sparse-view CT},
  author={Han, Yoseob and Ye, Jong Chul},
  journal={IEEE transactions on medical imaging},
  volume={37},
  number={6},
  pages={1418--1429},
  year={2018},
  publisher={IEEE}
}
```

