### Paper:

ADMM-based deep reconstruction for limited-angle CT

### Author:

Jiaxi Wang, Li Zeng, Chengxiang Wang and Yumeng Guo

### Year:

2019

### Notes:

阅读日期：2020.02.15

泛读。这篇文章研究的是 limited-angle CT 的重建，使用的方法是 ADMM 算法，用一个 U-net 替换其中的 proximal 算子（也就是正则项那一步）。CNN 是事先训练好的，网络结构也没有创新。不过由于作者的目标函数中 data fidelity 的范数计算带了权重，所以 data fidelity 那一步的计算很难算最优值，因此用了一个小技巧（参见后面的算法图）。总的来说没有什么参考价值。

<img src="http://latex.codecogs.com/svg.latex? \underset{f \geqslant 0}{\arg \min }\left\{\frac{1}{2}\|A f-g\|_{D}^{2}+\lambda_{1} R(f)\right\}" border="0"/>

这里的 $\|\cdot\|_{D} = \sqrt{\cdot^{T} D \cdot}$

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200215094245.png"/>

### Bibtex:

```latex
@article{wang2019admm,
  title={ADMM-based deep reconstruction for limited-angle CT},
  author={Wang, Jiaxi and Zeng, Li and Wang, Chengxiang and Guo, Yumeng},
  journal={Physics in Medicine \& Biology},
  volume={64},
  number={11},
  pages={115011},
  year={2019},
  publisher={IOP Publishing}
}
```

