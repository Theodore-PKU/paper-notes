### Paper:

KIKI-net: cross-domain convolutional neural networks for reconstructing undersampled magnetic resonance images

### Author:

Taejoon Eo, Dosik Hwang, Yohan Jun, Taeseong Kim, Jinseong Jang, Ho-Joon Lee

### Year:

2018

### Notes:

阅读日期：2020.02.09

泛读。这篇文章研究的是 MRI 的重建，用的方法是 CNN + data fidelity。CNN 在这里有两个，一个是 kspace 上的 CNN，一个是 image 上的 CNN，两者顺次链接，然后接 data fidelity，构成一个 block，重复该 block 得到重建结果。由于网络很深，所以训练的时候，逐个 CNN 训练，此前的网络部分就固定参数，kspace-CNN 和 image-CNN 都有各自对应的损失函数。两种 CNN 的网络结构基本一样，都是先将实部和虚部分别通过一个卷积层提取特征，合并后再经过多层卷积层，最后再用 1x1 卷积分离成两个 channel。对于 image-CNN 而言，还有一个 residual learning 的结构。

整体的流程图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200209211123.png" width="70%"/>

kspace-CNN 结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200209211051.png"/>

kspace 的损失函数：

<img src="http://latex.codecogs.com/svg.latex? \left\|\mathbf{k}-H_{\mathrm{k}}\left(\mathbf{k}_{\mathbf{u}} ; \mathbf{\theta}_{\mathbf{k}}\right)\right\|_{2}^{2}" border="0"/>

image-CNN 结构；

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200209211058.png"/>

image 的损失函数：

<img src="http://latex.codecogs.com/svg.latex? \underset{\theta_{\mathbf{x}}}{\operatorname{argmin}}\left\|\mathbf{x}-H_{\mathbf{x}}\left(\mathbf{x}_{\mathbf{u}} ; \boldsymbol{\theta}_{\mathbf{x}}\right)\right\|_{2}^{2}+\lambda\left\|\mathbf{k}_{\mathbf{u}}-\mathbf{U} \circ \mathcal{F}_{2 D}\left(H_{\mathbf{x}}\left(\mathbf{x}_{\mathbf{u}} ; \mathbf{\theta}_{\mathbf{x}}\right)\right)\right\|_{2}^{2}" border="0"/>

data fidelity:

<img src="http://latex.codecogs.com/svg.latex? \widehat{\mathbf{k}_{\mathbf{D}}}\left(\mathrm{k}_{\mathrm{x}}, \mathrm{k}_{\mathrm{y}}\right)=\left\{\begin{array}{cl}{\dfrac{\widehat{\mathbf{k}}_{\mathrm{ICNN}}\left(\mathrm{k}_{\mathrm{x}}, \mathrm{k}_{\mathrm{y}}\right)+\lambda \mathbf{k}_{\mathrm{u}}\left(\mathrm{k}_{\mathrm{x}}, \mathrm{k}_{\mathrm{y}}\right)}{1+\lambda}} & {\text { if } \mathbf{U}\left(\mathrm{k}_{\mathrm{x}}, \mathrm{k}_{\mathrm{y}}\right)=1} \\ {\widehat{\mathbf{k}}_{\mathrm{ICNN}}\left(\mathrm{k}_{\mathrm{x}}, \mathrm{k}_{\mathrm{y}}\right)} & {\text { if } \mathbf{U}\left(\mathrm{k}_{\mathrm{x}}, \mathrm{k}_{\mathrm{y}}\right)=0}\end{array}\right." border="0"/>

### Bibtex:

```latex
@article{eo2018kiki,
  title={KIKI-net: cross-domain convolutional neural networks for reconstructing undersampled magnetic resonance images},
  author={Eo, Taejoon and Jun, Yohan and Kim, Taeseong and Jang, Jinseong and Lee, Ho-Joon and Hwang, Dosik},
  journal={Magnetic resonance in medicine},
  volume={80},
  number={5},
  pages={2188--2201},
  year={2018},
  publisher={Wiley Online Library}
}
```

