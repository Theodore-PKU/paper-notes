### Paper:

Learning Generative ConvNets via Multi-grid Modeling and Sampling

### Author:

Ruiqi Gao, Yang Lu, Junpei Zhou, Song-Chun Zhu, Ying Nian Wu

### Year:

2018

### Notes:

阅读日期：2020.01.14

这篇文章的整体框架和以前UCLA（作者）的那几篇 文章是一样的，都是用基于能量模型的CNN来生成图像。利用 MCMC（实际是CD和朗之万动态）的方式来采样。这篇文章的创新点在于使用 multiple grid 的 方式，也就是生成不同分辨率的图像，感觉就是利用了图像金字塔。

算法：（n 应该表示不同的 grid）

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-14%E4%B8%8A%E5%8D%8811.44.52.png" width="50%"/>

### Bibtex:

```latex
@inproceedings{gao2018learning,
  title={Learning generative convnets via multi-grid modeling and sampling},
  author={Gao, Ruiqi and Lu, Yang and Zhou, Junpei and Zhu, Song-Chun and Nian Wu, Ying},
  booktitle={Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition},
  pages={9155--9164},
  year={2018}
}
```

