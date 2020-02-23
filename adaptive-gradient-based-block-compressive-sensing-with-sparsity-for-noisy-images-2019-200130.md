### Paper:

Adaptive gradient-based block compressive sensing with sparsity for noisy images

### Author:

Hui-Huang Zhao, Paul L. Rosin, Yu-Kun Lai,  Jin-Hua Zheng, Yao-Nan Wang

### Year:

2019

### Notes:

阅读时间：2020.01.30

泛读。这篇文章研究的是 BCS 的重建，作者的主要想法是自适应地选择合适的 block shape 和重建算法中的正则项的系数。然而作者使用的自适应方法是对所有实现给定的 block shape 和正则项系数一一进行计算，然后比较重建结果中稀疏性最大的作为最后的选择（作者似乎是在部分预先随机选择的区域上进行这个计算，但写的很不清楚，很多地方不确定具体怎么做，不过因为这个方法太垃圾了，所以不用在意也没关系）。

从算法的表述上看，可以这么理解：首先随机的选择一些 non-overlapping 的区域，这些区域的大小时固定，然后将每个区域用不同的 shape 进行分割，对这些子区域进行重建，计算稀疏的程度，选择稀疏程度最高的 shape，然后问题来了，整个图像是用一个 shape？不同图像的shape 不同？这个问题论文里没有说清楚。

lamda 的自适应选择也是在重建时，选择重建结果稀疏性最高的那一个，所以都是一个一个的尝试一遍。

给一个选择 shape 的算法过程，就可以看出作者有些地方没有写清楚了。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-30%E4%B8%8A%E5%8D%8810.49.39.png" width="80%"/>

### Bibtex:

```latex
@article{zhao2019adaptive,
  title={Adaptive gradient-based block compressive sensing with sparsity for noisy images},
  author={Zhao, Hui-Huang and Rosin, Paul L and Lai, Yu-Kun and Zheng, Jin-Hua and Wang, Yao-Nan},
  journal={Multimedia Tools and Applications},
  pages={1--23},
  year={2019},
  publisher={Springer}
}
```

