### Paper:

Optimizing a Parameterized Plug-and-Play ADMM for Iterative Low-Dose CT Reconstruction

### Author:

Ji He, Yan Yang, Yongbo Wang, Dong Zeng, Zhaoying Bian, Hao Zhang, Jian Sun , Zongben Xu, and Jianhua Ma

### Year:

2018

### Notes:

阅读日期：2020.02.22

泛读。这篇文章研究的是 low-dose CT 的重建。使用的方法其实还是 unroll ADMM 算法，只不过目标函数包含了 sinogram 的正则项。CNN 部分只替代了和图像的正则项有关的部分，用的是一个 resNet 的结构。没啥参考价值。

目标函数：

<img src="http://latex.codecogs.com/svg.latex? \min _{x, y} \frac{1}{2}\|y-\hat{y}\|_{\Sigma_{y}^{-1}}^{2}+\frac{1}{2}\|A x-y\|_{\Sigma_{x}^{-1}}^{2}+\lambda R_{y}(y)+\gamma R_{x}(x)" border="0"/>

$R_y(y)$ 没有用特殊的结构，是一个比较普通的 prior，$R_{y}=\frac{1}{2} \sum_{j} \sum_{m \in N_{j}} \omega_{j m}\left(y_{j}-y_{m}\right)^{2}$

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200222112512.png"/>

### Bibtex:

```latex
@article{he2018optimizing,
  title={Optimizing a parameterized plug-and-play ADMM for iterative low-dose CT reconstruction},
  author={He, Ji and Yang, Yan and Wang, Yongbo and Zeng, Dong and Bian, Zhaoying and Zhang, Hao and Sun, Jian and Xu, Zongben and Ma, Jianhua},
  journal={IEEE transactions on medical imaging},
  volume={38},
  number={2},
  pages={371--382},
  year={2018},
  publisher={IEEE}
}
```

