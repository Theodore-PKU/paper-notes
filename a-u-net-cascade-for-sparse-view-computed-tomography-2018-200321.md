### Paper:

A U-Nets Cascade for Sparse View Computed Tomography

### Author:

Andreas Koﬂer, Markus Haltmeier, Christoph Kolbitsch, Marc Kachelrieß , and Marc Dewey

### Year:

2018

### Notes:

泛读。这篇文章研究的是 sparse view 的 CT 重建。使用的方法是 unroll 形式的网络结构，由 U-net 和 data consistency layer 构成。U-net 的参数量比较少，属于一个较小型的网络。这篇文章和其他文章不一样的地方主要在于 data consistency 层，一般的做法都是考虑图像域，但是这篇文章考虑的是 sinogram，类似于 MRI 的 DC layer。不过 MRI 的 DC，用傅立叶拟变换什么的比较简单，对 CT 而言比较复杂一点。U-net 的作用仍然是去噪去伪影，或者说 prior 投影，DC 则是满足测量值。

记号：$S_I$ 表示 sparse view 的位置，$R$ 表示 forward operator，$R^{\dagger}$ 表示伪逆。所以 forward model 是
$$
\mathbf{R}_{I} \mathbf{x}=\mathbf{y}_{I}
$$
DC layer 的计算是：
$$
F_{\Theta, \mathbf{y}_{I}, \mathbf{x}_{\mathrm{cnn}}, \lambda}(\mathbf{z}) \triangleq\left\|\mathbf{R}\left(\mathbf{x}_{\mathrm{cnn}}\right)-\mathbf{z}\right\|_{2}^{2}+\lambda\left\|\mathbf{y}_{I}-\mathbf{S}_{I} \mathbf{z}\right\|_{2}^{2}
$$
这里的 $z$ 实际上就是 sinogram，所以计算出 $z$ 之后，还需要乘上 $R^{\dagger}$ 得到 DC layer 的对应图像。DC layer 的 $z$ 的解类似于 MRI 的 kspace 的解。

网络结构示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200321191907.png"/>

比较对象由 cascade-CNN（非 U-net 网）和单个 U-net。比较的评价指标由好几项，常见的 psnr 等作者提出的方法没有什么提升。但是在一个称为“基于 haar 小波的 perceptual similarity index” 的指标上，作者的网络效果最好。

### Bibtex:

```latex
@inproceedings{kofler2018u,
  title={A U-Nets cascade for sparse view computed tomography},
  author={Kofler, Andreas and Haltmeier, Markus and Kolbitsch, Christoph and Kachelrie{\ss}, Marc and Dewey, Marc},
  booktitle={International Workshop on Machine Learning for Medical Image Reconstruction},
  pages={91--99},
  year={2018},
  organization={Springer}
}
```

### 其他

关于 CT 的 forward 和伪逆的计算参考这里的代码

Jonas, A.: ODL - operator discretization library (2013). https://github.com/odlgroup/odl
