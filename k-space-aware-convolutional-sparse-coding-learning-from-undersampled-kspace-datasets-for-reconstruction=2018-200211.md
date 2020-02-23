### Paper:

k-space Aware Convolutional Sparse Coding: Learning from Undersampled k-space Datasets for Reconstruction

### Author:

Frank Ong and Michael Lustig

### Year:

2018

### Notes:

阅读日期：2020.02.11

泛读。这篇文章研究的是 MRI 的重建，使用的方法是 convolutional sparse coding，不过和其他类似的方法的区别在于，作者使用的数据集是 under-sampled dataset。换句话说，只有部分 kspace 的数据是 groundtruth，因此作者设计的优化目标函数类似于 DIP 的做法，及在 kspace 域上极小化 2 范数（当然还有一项是 coding 的 1 范数）。作者的实验显示用 under-sampled 数据集学到的字典和 full-sample 数据集的学到的字典是相似的。效果比 l1 范数的小波变换方法好。重建的时候学习到的卷积参数是固定的，卷积参数是 256 个 11x11 卷积。

目标函数：

<img src="http://latex.codecogs.com/svg.latex? \min _{\alpha_{i j}, d_{j} ;\|d\|_{2} \leq 1} \frac{1}{2 m} \sum_{i=1}^{m}\left\|A_{i}\left(\sum_{j=1}^{n} d_{j} * \alpha_{i j}\right)-y_{i}\right\|_{2}^{2}+\lambda\left\|\alpha_{i j}\right\|_{1}" border="0"/>

优化仍然采用交替优化的方法，但没有具体说明每个子问题的优化方法。

### Bibtex:

```latex
@inproceedings{ong2018k,
  title={k-space Aware Convolutional Sparse Coding: Learning from Undersampled k-space Datasets for Reconstruction},
  author={Ong, Frank and Lustig, Michael},
  booktitle={Proc ISMRM},
  year={2018}
}
```

