### Paper:

Multi-Scale Learned Iterative Reconstruction

### Author:

Andreas Hauptmann, Jonas Adler, Simon Arridge, and Ozan Öktem

### Year:

2019

### Notes:

阅读日期：2020.02.29

泛读。这篇文章研究的是逆问题。本文的思想是利用测量矩阵的 scale invariant 性质（只适用于这种情况，比如随机高斯矩阵这种就不行）来减少计算量。因此在unroll network 中离散化的粒度是逐渐变细的，最后一层才是 full 高分辨率 forward 算子。这样的好处是在低分辨率的情况下，测量矩阵等的相关计算就比较少。作者先用这个想法设计一个迭代的算法，然后把这个想法拓展到一个 U-net。

设不同粒度的 forward 模型可以写成：

![](http://latex.codecogs.com/svg.latex? g_{i}=\mathcal{A}_{i}\left(f_{i}^{*}\right)+\delta g_{i})

迭代的过程就可以写成：

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}f_{i}=\Lambda_{\theta_{i}}\left(\tilde{f}_{i}, \nabla \mathcal{D}_{i}\left(\tilde{f}_{i} ; g\right)\right) \\ \tilde{f}_{i+1}=\tau_{i+1}\left(f_{i}\right)\end{array}\right."/>

<img src="http://latex.codecogs.com/svg.latex? \nabla \mathcal{D}_{i}\left(f_{i} ; g\right):=\mathcal{A}_{i}^{*}\left(\mathcal{A}_{i}\left(f_{i}\right)-\pi_{i}(g)\right)" />

这里的 $\tau_{i+1}$ 是上采样，$\pi_{i+1}$ 是对原始测量值在不同分辨率下的修正。

对应作者设计的一个 U-net：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200301142831.png"/>

关于这里的网络结构，有一些疑问，不知道作者是否使用的是 cascade 的结构，只有 scale 4 使用了完整的 U-net。

### Bibtex:

```latex
@article{hauptmann2019multi,
  title={Multi-Scale Learned Iterative Reconstruction},
  author={Hauptmann, Andreas and Adler, Jonas and Arridge, Simon and {\"O}ktem, Ozan},
  journal={arXiv preprint arXiv:1908.00936},
  year={2019}
}
```

