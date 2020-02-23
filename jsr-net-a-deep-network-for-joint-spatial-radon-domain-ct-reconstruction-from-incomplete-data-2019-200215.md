### Paper:

JSR-Net: A Deep Network for Joint Spatial-radon Domain CT Reconstruction from Incomplete Data

### Author:

Haimiao Zhang, Bin Dong, Baodong Liu

### Year:

2019

### Notes:

阅读日期：2020.02.15

泛读。这篇文章研究的是 Sparse view CT 的重建，使用的方法是将 JSR 模型展开成网络的形式。原来的算法中有两个矩阵逆的计算以及 thresholding 算子，作者将其替换成了神经网络的计算，矩阵逆的计算用的网络结构是 3 level DenseNet + LM-ResNet structure，thresholding 算子用的是一个较简单的三层卷积神经网络。损失函数比较复杂，有好几项。

JSR 模型的目标函数：

<img src="http://latex.codecogs.com/svg.latex? \min _{\boldsymbol{u}, \boldsymbol{f}} \mathcal{F}(\boldsymbol{u}, \boldsymbol{f}, \boldsymbol{Y})+\left\|\boldsymbol{\lambda}_{1} \boldsymbol{W}_{1} \boldsymbol{u}\right\|_{1,2}+\left\|\boldsymbol{\lambda}_{2} \boldsymbol{W}_{2} \boldsymbol{f}\right\|_{1,2}" border="0"/>

其中：

<img src="http://latex.codecogs.com/svg.latex? \mathcal{F}(\boldsymbol{u}, \boldsymbol{f}, \boldsymbol{Y}) = \frac{1}{2}\left\|R_{\Gamma^{c}}(\boldsymbol{f}-\boldsymbol{Y})\right\|^{2}+\frac{\alpha}{2}\left\|R_{\Gamma}(\mathcal{P} \boldsymbol{u}-\boldsymbol{f})\right\|^{2}+\frac{\gamma}{2}\left\|R_{\Gamma^{c}}(\mathcal{P} \boldsymbol{u}-\boldsymbol{Y})\right\|^{2}" border="0"/>

$\Gamma^c$ 表示的是采样的角度。这里的 joint 可以理解为同时在 image 和 sinogram 两个域都有约束，不过这里的 1,2 范数不知道怎么计算的。

原始问题用的是 ADMM 的算法框架。下图的左边是传统算法，右边是带网络的算法，可以发现只是替换了。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200216111613.png"/>

损失函数是：

<img src="http://latex.codecogs.com/svg.latex? \mathcal{L}_{S S 2}=\theta_{1} \mathcal{L}_{S S I M}+\mathcal{L}_{M S E}+\theta_{3} \mathcal{L}_{s e m}" border="0"/>

<img src="http://latex.codecogs.com/svg.latex?\mathcal{L}_{S S I M}=\sum\left(1-S S I M\left(\boldsymbol{u}_{r e c}, \boldsymbol{u}_{t r u t h}\right)\right)" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \mathcal{L}_{M S E}= \mathcal{L}_{S} + \mathcal{L}_{R}, \mathcal{L}_{S}=\sum\left\|\boldsymbol{u}_{r e c}-\boldsymbol{u}_{t r u t h}\right\|^{2}, \mathcal{L}_{R} = \sum\left\|R_{\Gamma^{c}} \mathcal{P}\left(u_{r e c}-u_{t r u t h}\right)\right\|^{2}" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \mathcal{L}_{s e m}=\left\|\operatorname{sem}\left(\boldsymbol{u}_{r e c}\right)-\operatorname{sem}\left(\boldsymbol{u}_{t r u t h}\right)\right\|^{2}" border="0"/>

$\mathcal{L}_{s e m}$ 和水平集有关，但是不懂怎么计算。

### Bibtex:

```latex
@inproceedings{zhang2019jsr,
  title={JSR-Net: a deep network for joint spatial-radon domain CT reconstruction from incomplete data},
  author={Zhang, Haimiao and Dong, Bin and Liu, Baodong},
  booktitle={ICASSP 2019-2019 IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP)},
  pages={3657--3661},
  year={2019},
  organization={IEEE}
}
```

