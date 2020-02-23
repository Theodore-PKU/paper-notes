### Paper:

Deep Learning with Domain Adaptation for Accelerated Projection-Reconstruction MR

### Author:

Yoseob Han, Jaejun Yoo, Hak Hee Kim, Hee Jung Shin, Kyunghyun Sung, and Jong Chul Ye

### Year:

2018

### Notes:

阅读日期：2020.01.15

泛读。这篇文章研究的是 radial kspace trajectory 的采样方式下的MR重建。作者使用的方法是 domain adaption。作者分析了 radial 采样相较于笛卡尔采样的优势，同时指出 radial 采样的训练数据很少。考虑到 radial 采样和 CT 的相似性（也有用 FBP 来重建 radial 采样的 MRI），并且两者的伪影也非常相似，因此作者考虑使用 CT（或者，更进一步用合成的MR数据）来预训练网络，然后再用少量的 radial MRI 数据 fine-tune 网络。因为伪影具有全局性，所以作者使用的网络结构和 CT 去伪影（作者之前的文章）一样，都是多尺度的具有 residual 结构的 U-Net。作者表示还可以使用更新的 domain adaption 的方法。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-15%E4%B8%8B%E5%8D%887.56.01.png"/>

### Bibtex:

```latex
@article{han2018deep,
  title={Deep learning with domain adaptation for accelerated projection-reconstruction MR},
  author={Han, Yoseob and Yoo, Jaejun and Kim, Hak Hee and Shin, Hee Jung and Sung, Kyunghyun and Ye, Jong Chul},
  journal={Magnetic resonance in medicine},
  volume={80},
  number={3},
  pages={1189--1205},
  year={2018},
  publisher={Wiley Online Library}
}
```

