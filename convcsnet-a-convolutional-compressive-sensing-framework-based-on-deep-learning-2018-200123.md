### Paper:

ConvCSNet: A Convolutional Compressive Sensing Framework Based on Deep Learning

### Author:

Xiaotong Lu, Weisheng Dong,  Peiyao Wang, Guangming Shi, and Xuemei Xie

### Year:

2018

### Notes:

阅读时间：2020.01.23

泛读。这篇文章研究的是 CS 的图像重建。作者使用的方法是卷积网络，包括感知采样部分和重建部分。采样部分使用 m 个 LxL，stride=s 的卷积层。重建部分作者采用了 TL 的传统算法展开的方式设计网络结构。在 TL 的子问题中，作者使用了简化的手段。

<img src="http://latex.codecogs.com/svg.latex? \min _{\boldsymbol{x}, \boldsymbol{\alpha}_{k}}\|\boldsymbol{y}-\boldsymbol{\Phi} \boldsymbol{x}\|_{2}^{2}+\eta \sum_{k}^{K}\left\{\left\|\boldsymbol{w}_{k} * \boldsymbol{x}-\boldsymbol{\alpha}_{k}\right\|_{F}^{2}+J\left(\boldsymbol{\alpha}_{k}\right)\right\}" border="0"/>

用交替迭代法求解的子问题是：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \boldsymbol{x}=& \underset{\boldsymbol{x}}{\operatorname{argmin}}\|\boldsymbol{y}-\boldsymbol{\Phi} \boldsymbol{x}\|_{2}^{2}+\eta \sum_{k}^{K}\left\{\left\|\boldsymbol{w}_{k} * \boldsymbol{x}-\boldsymbol{\alpha}_{k}\right\|_{F}^{2}\right\} \\ \boldsymbol{\alpha}_{k} &=\underset{\boldsymbol{\alpha}}{\operatorname{argmin}}\left\|\boldsymbol{w}_{k} * \boldsymbol{x}-\boldsymbol{\alpha}_{k}\right\|_{F}^{2}+J\left(\boldsymbol{\alpha}_{k}\right) \end{aligned}" border="0"/>

其中，第二个子问题有闭式解：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{\alpha}_{k}^{(t+1)}=\mathcal{S}_{\tau}\left(\boldsymbol{w}_{k} * \boldsymbol{x}^{(t)}\right), \text { or }\left[\boldsymbol{w}_{k} * \boldsymbol{x}^{(t)}\right]_{+}" border="0"/>

第一个子问题没有，如果用迭代法求，可以写成下式：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \boldsymbol{x}^{(t+1)}= \boldsymbol{x}^{(t)}-\delta\left(\boldsymbol{\Phi}^{\top}\left(\mathbf{\Phi} \boldsymbol{x}^{(t)}-\boldsymbol{y}\right)+ \eta \sum_{k}^{K}\left(\mathbf{W}_{k}^{\top}\left(\mathbf{W}_{k} \boldsymbol{x}^{(t)}-\boldsymbol{\alpha}_{k}^{(t+1)}\right)\right)\right) \end{aligned}" border="0"/>

通过简化和假设，作者化简为：

<img src="http://latex.codecogs.com/svg.latex? x^{(t+1)} \approx \rho \boldsymbol{x}^{(t)}+\delta \boldsymbol{x}^{(0)}+\gamma \boldsymbol{x}^{(t+1 / 2)}" border="0"/>

其中，$\boldsymbol{x}^{(t+1 / 2)}=\sum_{k}^{K} \mathbf{W}_{k}^{\top} \boldsymbol{\alpha}_{k}^{(t+1)}$。通过这个方式，作者把展开的迭代算法变成了网络结构。对于卷积的部分，使用了3x3的卷积网络。而第一个子问题的更新方式就变成了残差结构。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-23%E4%B8%8A%E5%8D%8811.31.51.png"/>

### Bibtex:

```latex
@article{lu2018convcsnet,
  title={Convcsnet: A convolutional compressive sensing framework based on deep learning},
  author={Lu, Xiaotong and Dong, Weisheng and Wang, Peiyao and Shi, Guangming and Xie, Xuemei},
  journal={arXiv preprint arXiv:1801.10342},
  year={2018}
}
```

