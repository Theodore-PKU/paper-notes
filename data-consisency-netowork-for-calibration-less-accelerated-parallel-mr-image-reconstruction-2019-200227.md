### Paper:

Data consistency networks for (calibration-less) accelerated parallel MR image reconstruction

### Author:

Jo Schlemper, Jinming Duan, Cheng Ouyang, Chen Qin, Jose Caballero, Joseph V. Hajnal, and Daniel Rueckert

### Year:

2019

### Notes:

阅读日期：2020.02.27

泛读。这篇文章研究的是 DC-CNN 的拓展，作者原来提出的方法是针对 single coil MRI 重建，这篇文章则是拓展到 multi-coil 的重建。DC-CNN 本来的做法是 DC 和 CNN 交替的 cascade 网络结构。现在考虑多线圈，主要关注的就是 DC 的改进。作者给出了两种方案，第一种是先估计出 sensitive map 后先合成出 single image，在 DC 利用 sensitive map 展开成不同的 coil，然后各自计算 DC，最后再合并起来作为下一个 CNN 的输入；第二种则是在网络的主题部分每个 coil 都对应不同的通道，DC 也是单独计算，这样在 cascade 部分不需要考虑 sensitive map，只是在最后的损失函数中，将 sensitive map 作为一个权重来计算损失函数。第二种在论文种被称为 calibration-less。

DC 的计算比较普通，没什么特别的：

<img src="http://latex.codecogs.com/svg.latex? s_{\mathrm{rec}}^{(i)}(k)=\left\{\begin{array}{ll}\lambda s_{\mathrm{cnn}}^{(i)}(k)+(1-\lambda) s_{0}^{(i)}(k) & \text { if } k \in \Omega \\ s_{\mathrm{cnn}}^{(i)}(k) & \text { if } k \notin \Omega\end{array}\right." border="0"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200227213533.png"/>

损失函数分别是：

<img src="http://latex.codecogs.com/svg.latex? \ell_{2}(\theta)=\sum_{\left(x_{u}, x\right) \in D}\left\|x-f_{\mathrm{cnn}}\left(x_{u} ; \theta\right)\right\|_{2}^{2}" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \ell_{2}(\theta)=\sum_{\left(x_{u}, x\right) \in D} \sum_{i=1}^{n_{\text {coil }}}\left\|C_{i}^{H}\left(x_{i}-f_{\operatorname{cnn}}\left(x_{u, 1}, \ldots, x_{u, n_{\text {coil }}} ; \theta\right)_{i}\right)\right\|_{2}^{2}" border="0"/>

### Bibtex:

```latex
@article{schlemper2019data,
  title={Data consistency networks for (calibration-less) accelerated parallel MR image reconstruction},
  author={Schlemper, Jo and Duan, Jinming and Ouyang, Cheng and Qin, Chen and Caballero, Jose and Hajnal, Joseph V and Rueckert, Daniel},
  journal={arXiv preprint arXiv:1909.11795},
  year={2019}
}
```

