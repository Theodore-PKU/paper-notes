### Paper:

Hyperspectral Image Reconstruction Using a Deep Spatial-Spectral Prior

### Author:

Lizhi Wang, Chen Sun, Ying Fu, Min H. Kim, Hua Huang

### Year:

2019

### Notes:

阅读时间：2020.02.02

泛读。这篇文章研究的是 hyperspectral image 的重建，使用的方法是传统算法展开的网络形式，网络的主要目的是作为 proximal 算子。hyperspectral image 的重建，相当于用原始的 3D 图像（谱信号）的 2D 感知图像（用某种线性变换）恢复出原来的 3D 图像。和一般的 2D 图像不同，还有一个谱的维度，所以作者在设计 proximal 算子的时候，考虑了这一点。proximal 算子的设计相当于隐式地表达了 prior。不过作者提出的神经网络结构比较简单。传统算法中的另外一步本来是由闭解，但作者改成了一步 CG 算法。

问题描述：在考虑 patch 的情况下，

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{g}_{i}=\mathbf{\Phi}_{i} \boldsymbol{f}_{i}" border="0"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-02%E4%B8%8B%E5%8D%881.44.37.png" width="80%"/>

原始目标函数：

<img src="http://latex.codecogs.com/svg.latex? \hat{\boldsymbol{f}}=\arg \min _{\boldsymbol{f}}\|\boldsymbol{g}-\boldsymbol{\Phi} \boldsymbol{f}\|^{2}+\tau R(\boldsymbol{f})" border="0"/>

$R(\boldsymbol{f})$ 表示 prior 正则项。作者使用的是 split variable 的算法，因此迭代的步骤为：

1. data fidelity

<img src="http://latex.codecogs.com/svg.latex? \hat{\boldsymbol{f}}^{(k+1)}=\arg \min _{\boldsymbol{f}}\|\boldsymbol{g}-\boldsymbol{\Phi} \boldsymbol{f}\|^{2}+\eta\left\|\boldsymbol{h}^{(k)}-\boldsymbol{f}\right\|^{2}" border="0"/>

单步 CG 算法：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \boldsymbol{f}^{(k+1)} &=\boldsymbol{f}^{(k)}-\epsilon\left[\boldsymbol{\Phi}^{\top}\left(\boldsymbol{\Phi} \boldsymbol{f}^{(k)}-\boldsymbol{g}\right)+\eta\left(\boldsymbol{f}^{(k)}-\boldsymbol{h}^{(k)}\right)\right] \\ &=\overline{\mathbf{\Phi}} \boldsymbol{f}^{(k)}+\epsilon \boldsymbol{f}^{(0)}+\epsilon \eta \boldsymbol{h}^{(k)} \end{aligned}" border="0"/>

2. proximal

<img src="http://latex.codecogs.com/svg.latex? \hat{\boldsymbol{h}}^{(k+1)}=\arg \min _{\boldsymbol{h}}\left\|\boldsymbol{h}-\boldsymbol{f}^{(k+1)}\right\|^{2}+\frac{\tau}{\eta} R(\boldsymbol{h})" border="0"/>

用网络求解：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{h}^{(k+1)}=S\left(\boldsymbol{f}^{(k+1)}\right)" border="0"/>

综合起来的网络迭代过程：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{f}^{(k+1)}=\overline{\mathbf{\Phi}} \boldsymbol{f}^{(k)}+\epsilon \boldsymbol{f}^{(0)}+\epsilon \eta S\left(\boldsymbol{f}^{(k)}\right)" border="0"/>

示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-02%E4%B8%8B%E5%8D%881.49.44.png"/>

损失函数使用了 2 范数的损失函数。

### Bibtex:

```latex
@inproceedings{wang2019hyperspectral,
  title={Hyperspectral Image Reconstruction Using a Deep Spatial-Spectral Prior},
  author={Wang, Lizhi and Sun, Chen and Fu, Ying and Kim, Min H and Huang, Hua},
  booktitle={Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition},
  pages={8032--8041},
  year={2019}
}
```

