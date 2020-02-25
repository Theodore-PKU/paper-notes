### Paper:

Deep Plug-and-Play Prior for Parallel MRI Reconstruction

### Author:

Ali Pour Yazdanpanah, Onur Afacan, Simon K. Warﬁeld

### Year:

2019

### Notes:

阅读日期：2020.02.25

泛读。这篇文章研究的是 parallel mri 的重建。使用的方法按照标题的说法是 plug-and-play prior，其实就是 ADMM 算法中将正则项相关的 proximal 算子用 DNN（U-net）替换（可以参考下图）。所谓 plug-and-play prior，其实也可以看成是 unroll 形式的网络。另外 sensitive map 应该是事先估计好的。

原始目标函数：

<img src="http://latex.codecogs.com/svg.latex? \hat{\mathbf{x}}=\underset{\mathbf{x}}{\operatorname{argmin}} \frac{1}{2}\|\mathrm{d}-\operatorname{PFSx}\|_{2}^{2}+\beta \mathbf{R}(\mathbf{x})" border="0"/>

对应的 ADMM 展开网络算法：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200225114944.png" width="60%"/>

其中：

<img src="http://latex.codecogs.com/svg.latex? \operatorname{prox}(\mathrm{d}, \mathrm{S}, \widetilde{\mathrm{x}} ; \lambda)=\operatorname{argmin}_{\mathrm{z}} \frac{1}{2}\|\mathrm{z}-\widetilde{\mathrm{x}}\|_{2}^{2}+\frac{\lambda}{2}\|\mathrm{PFSz}-\mathrm{d}\|_{2}^{2}" border="0"/>

整体流程图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200225114427.png"/>

### Bibtex:

```latex
@inproceedings{pour2019deep,
  title={Deep Plug-and-Play Prior for Parallel MRI Reconstruction},
  author={Pour Yazdanpanah, Ali and Afacan, Onur and Warfield, Simon},
  booktitle={Proceedings of the IEEE International Conference on Computer Vision Workshops},
  pages={0--0},
  year={2019}
}
```

