### Paper:

An improved method for single image super-resolution based on deep learning

### Author:

Chao Xie, Ying Liu, Weili Zeng, Xiaobo Lu

### Year:

2019

### Notes:

阅读时间：2020.02.03

泛读。这篇文章研究的是超分辨问题。作者的想法就是先对 LR 图像先进行分解，分解出一个 smooth 的图像和 residual 的图像。分解的方法，论文里说是 CSC，但其实就是一个带正则项的平均平滑卷积近似。对于 residual 部分，用一个神经网络进行 enhance，对于 smooth 部分，用一个插值算法进行插值，不如双线性插值。最后把两个部分加起来。

smooth 部分的计算方法为极小化下式：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \boldsymbol{S}_{Y}=& \boldsymbol{f} * \mathbf{Z}, \text { s.t. arg } \min \\ &\left\{\|\boldsymbol{Y}-\boldsymbol{f} * \mathbf{Z}\|_{2}^{2}+\phi\|\boldsymbol{h} * \mathbf{Z}\|_{2}^{2}+\varphi\|\boldsymbol{v} * \mathbf{Z}\|_{2}^{2}\right\} \end{aligned}" border="0"/>

这个式子有解（利用傅立叶变换）：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{Z}=\mathcal{F}^{-1}\left(\frac{\overline{\mathcal{F}^{f}} \cdot \mathcal{F}^{Y}}{\overline{\mathcal{F}^{f}} \cdot \mathcal{F}^{f}+\phi \overline{\mathcal{F}^{h}} \cdot \mathcal{F}^{h}+\varphi \overline{\mathcal{F}^{v}} \cdot \mathcal{F}^{v}}\right)" border="0"/>

得到 $\mathbf{Z}$ 之后，进而得到 $\mathbf{Y}$，也就是 smooth 的部分，residual 的部分就是 LR 图像减去 smooth 图像

网络示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-03%E4%B8%8B%E5%8D%881.45.34.png"/>

### Bibtex:

```latex

```

