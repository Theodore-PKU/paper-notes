### Paper:

Spatio-Temporal Deep Learning-Based Undersampling Artefact Reduction for 2D Radial Cine MRI with Limited Training Data

### Author:

Andreas Koﬂer, Marc Dewey, Tobias Schaeffter, Christian Wald, and Christoph Kolbitsch

### Year:

2019

### Notes:

阅读时间：2020.02.02

泛读。这篇文章研究的是 3D 的 MRI 重建。作者的方法本质上还是一个 artefacts removal 的方法，创新的地方在于输入既不是 2D 的 slice，也不是 3D 的 volumn，而是两张 2D slice，一个是空间层面，一个是时间层面（见下图）。使用的网络结构是 U-Net。作者在这篇文章里比较重点的分析了是否使用 residual 训练的问题。首 先，作者也使用了 Persistent homology 的分析，但是得出的结果和之前 Chong Chul Ye 的结论不一样，作者表示图像域的流形维度更低，不知道是不是因为时间域的变化比较小的缘故... 另外，作者讨论了训练时的 target 选择残差图像还是真实图像，对于前者，就相当于训练一个没有残差学习结构的网络。除了输入上，作者的选择可以增加训练的数据量以外，就没有什么特别之处了。

和其他方法的输入对比：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-02%E4%B8%8B%E5%8D%887.28.01.png"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-02%E4%B8%8B%E5%8D%887.28.33.png" width="50%"/>

设 $r_I = x_I - x$，带下标的表示有 artefacts，一种训练时 $CNN(x_I) + x_I \rightarrow r_I$，一种是 $CNN(x_I)  \rightarrow x$，这两个是等价的。不知道为什么还要再加一个残差学习的结构。损失函数是 2 范数，作者这篇文章很大的篇幅都在讲实验的内容。

### Bibtex:

```latex
@article{kofler2019spatio,
  title={Spatio-temporal deep learning-based undersampling artefact reduction for 2d radial cine mri with limited training data},
  author={Kofler, Andreas and Dewey, Marc and Schaeffter, Tobias and Wald, Christian and Kolbitsch, Christoph},
  journal={IEEE transactions on medical imaging},
  year={2019},
  publisher={IEEE}
}
```

