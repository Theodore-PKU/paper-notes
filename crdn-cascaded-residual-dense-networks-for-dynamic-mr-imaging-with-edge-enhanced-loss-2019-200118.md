### Paper:

CRDN: Cascaded Residual Dense Networks for Dynamic MR Imaging with Edge-enhanced Loss Constraint

### Author:

Ziwen Ke, Shanshan Wang, Huitao Cheng, Qiegen Liu, Leslie Ying, Hairong Zheng and Dong Liang

### Year:

2019

### Notes:

阅读日期：2020.01.18

泛读。这篇文章研究的是动态 MRI 重建，不过文章中作者似乎没有提到多帧的处理。整体结构上 cascade 的网络展开，作者和做法和原始的 cascade 的方法的差别在于三点。1. 网络的初始输入使用作者此前提出的 KPN 网络（kspace 上的网络）的输出；2. 每一个 residual block 使用了 dense 连接；3. 增加了 TV loss。作者在实验中还尝试了不同的 TV loss。总体来讲，即使是网络结构，也没有特别的参考意义。

网络结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-18%E4%B8%8B%E5%8D%881.59.53.png"/>

### Bibtex:

```latex
@article{ke2019crdn,
  title={CRDN: Cascaded Residual Dense Networks for Dynamic MR Imaging with Edge-enhanced Loss Constraint},
  author={Ke, Ziwen and Wang, Shanshan and Cheng, Huitao and Ying, Leslie and Liu, Qiegen and Zheng, Hairong and Liang, Dong},
  journal={arXiv preprint arXiv:1901.06111},
  year={2019}
}
```

