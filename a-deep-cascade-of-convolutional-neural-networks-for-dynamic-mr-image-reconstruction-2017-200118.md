### Paper:

A Deep Cascade of Convolutional Neural Networks for Dynamic MR Image Reconstruction

### Author:

Jo Schlemper, Jose Caballero, Joseph V. Hajnal, Anthony N. Price, and Daniel Rueckert

### Year:

2017

### Notes:

阅读日期：2020.01.18

精读。这篇文章研究的是动态 MRI 重建。作者使用的方法是 cascade 结构的网络。作者将重建问题当作是 de-aliasing 问题和 data fidelity 两个子问题，前者用神经网络实现，后者用常见的 data fidelity 的处理办法。因为是动态 MRI，如果考虑多帧同时训练，作者使用了 3D 卷积（如果是单张图片生成就无需考虑这部分），并且在 data fidelity 层，考虑了一般动态mri重建中使用的 data sharing 方法（即用相邻帧的kspace 数据来填补当前帧的缺失kspace 系数）

data fidelity 项的数学表达：

<img src="http://latex.codecogs.com/svg.latex? \min _{\mathbf{x}}\left\|\mathbf{x}-f_{\operatorname{cnn}}\left(\mathbf{x}_{u} | \boldsymbol{\theta}\right)\right\|_{2}^{2}+\lambda\left\|\mathbf{F}_{u} \mathbf{x}-\mathbf{y}\right\|_{2}^{2}" border="0"/>

这个闭式解就是 kspace 上的填补，$s_{rec}$ 是重建的kspace 系数。（作者还分析了 data fidelity 对应的求导计算）

<img src="http://latex.codecogs.com/svg.latex? \mathrm{s}_{\mathrm{rec}}(j)=\left\{\begin{array}{ll}{\mathbf{s}_{\mathrm{cnn}}(j)} & {\text { if } j \notin \Omega} \\{\frac{\mathbf{s}_{\mathrm{cnn}}(j)+\lambda \mathbf{s}_{0}(j)}{1+\lambda}} & {\text { if } j \in \Omega}\end{array}\right." border="0"/>

关于多帧的data sharing，作者的具体做法是，对输出的所有帧图像（某个CNN的输出，多个通道），先通过 kspace 的sharing 调整结果（只对未采样的kspace 系数做填补，用相邻帧的kspac数据的平均），这部分就是将原来的 data fidelity 层稍微改变一下，适应动态 mri 的情形。调整完之后再作为下一个CNN 的输入。

网络的初始输入是zero-fill重建。训练时的损失函数是图像的2范数。复数使用双通道的方式处理。

整体的网络结构（每个 CNN 还包含 residual learning 的结构）

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-18%E4%B8%8A%E5%8D%8810.48.12.png"/>

实验方面，作者做了很多控制变量的实验，探究不同设定的表现差异。不过这方面的结果基本符合一般的直觉。

> CNN 充当的就是一个 proximal 算子。

### Bibtex:

```latex
@article{schlemper2017deep,
  title={A deep cascade of convolutional neural networks for dynamic MR image reconstruction},
  author={Schlemper, Jo and Caballero, Jose and Hajnal, Joseph V and Price, Anthony N and Rueckert, Daniel},
  journal={IEEE transactions on Medical Imaging},
  volume={37},
  number={2},
  pages={491--503},
  year={2017},
  publisher={IEEE}
}
```

