### Paper:

DIMENSION: Dynamic MR Imaging with Both K-space and Spatial Prior Knowledge Obtained via Multi-Supervised Network Training

### Author:

Shanshan Wang, Ziwen Ke, Huitao Cheng, Sen Jia, Leslie Ying, Hairong Zheng and Dong Liang

### Year:

2019

### Notes:

阅读日期：2020.02.16

泛读。这篇文章研究的是动态 MRI 的重建。不过作者似乎是直接用 3D 卷积操作来处理 mulit-image 的情形了。作者的方法是深度学习，用两个网络 cascade 构成一个重建的网络。第一个网络 FDN 是处理 kspace 系数，输入是 undersample 的 kspace 系数。然后通过 IFFT 得到图像，作为第二个网络 SDN 的输入，第二个网络处理图像，输出是最终的重建结果。两个网络都是由多个 block 构成，每个 block 都是多层3D卷积网络，而且最后都有一个 data fidelity 的层，FDN 和 SDN 会有一些区别，不过都是 kspace 上的加权，属于普通的做法。另外 SDN 的 block 有 residual 的结构。关于损失函数，其实也很简单，就是 final reconstruction 的 image loss + FDN 的每个 block 的输出和 kspace 的真值的 mse + SDN 的每个 block 的输出和 image 的真值的 mse。作者的实验倒是蛮多的，但是看了之后也没啥有特点的地方，都是诸如比较有没有 kspace loss 的效果的差异。

两个 data fidelity层。

KDC 层：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{K}_{\mathrm{F}_{n e t m}}^{D C}=\left\{\begin{array}{ll}{\frac{\mathbf{K}_{\text {Fratm }}^{L}+\lambda \mathbf{K}_{u}\left(k_{x}, k_{y}\right)}{1+\lambda},} & {\text { if }\left(k_{x}, k_{y}\right) \in \Omega} \\ {\mathbf{K}_{\mathrm{F}_{n e t m}}^{L},} & {\text { if }\left(k_{x}, k_{y}\right) \notin \Omega}\end{array}\right." border="0"/>

IDC 层：

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{ll}{\mathbf{k}_{\mathbf{S}_{n}}=} & {\operatorname{FFT}\left(\mathbf{S}_{n}\right)} \\ {\hat{\mathbf{k}}_{\mathbf{S}_{n}}=} & {\left\{\begin{array}{ll}{\frac{\mathbf{k} \mathbf{S}_{n}\left(k_{x}, k_{y}\right)+\lambda \mathbf{k}_{u}}{1+\lambda},} & {\text { if }\left(k_{x}, k_{y}\right) \in \Omega} \\ {\mathbf{k}_{\mathbf{S}_{n}}\left(k_{x}, k_{y}\right),} & {\text { if }\left(k_{x}, k_{y}\right) \notin \Omega}\end{array}\right.} \\ {\hat{\mathbf{S}}_{n}=\operatorname{IFFT}\left(\hat{\mathbf{k}}_{\mathbf{S}_{n}}\right)}\end{array}\right." border="0"/>

损失函数：

<img src="http://latex.codecogs.com/svg.latex? \text { Tloss }=\left\|\mathbf{S}-\hat{\mathbf{S}}_{N}\right\|_{2}^{2}+\sum_{m=1}^{M} \alpha_{m}\left\|\mathbf{K}_{f}-\mathbf{K}_{\mathbf{F}_{n e t m}}^{D C}\right\|_{2}^{2}+\sum_{n=1}^{N-1} \beta_{n}\left\|\mathbf{S}-\hat{\mathbf{S}}_{n}\right\|_{2}^{2}" border="0"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200216210125.png"/>

### Bibtex:

```latex
@article{wang2019dimension,
  title={DIMENSION: Dynamic MR imaging with both k-space and spatial prior knowledge obtained via multi-supervised network training},
  author={Wang, Shanshan and Ke, Ziwen and Cheng, Huitao and Jia, Sen and Ying, Leslie and Zheng, Hairong and Liang, Dong},
  journal={NMR in Biomedicine},
  pages={e4131},
  year={2019},
  publisher={Wiley Online Library}
}
```

