### Paper:

Multispectral and Hyperspectral Image Fusion by MS/HS Fusion Net

### Author:

Qi Xie, Minghao Zhou, Qian Zhao, Deyu Meng, Wangmeng Zuo, Zongben Xu

### Year:

2019

### Notes:

阅读日期：2020.02.18

泛读。这篇文章研究的是 hyperspectral 图像的重建，使用的方法是展开一个 proximal 算法。作者首先为重建问题建立了一个模型，利用的是矩阵的一个分解。在这个分解中，有两个相关的矩阵都是未知的，在假设已知的情况下，需要顾及的是其中某一个矩阵，基于这样的假设给出了一个求解算法，求解的方法是 proximal graident 方法。然后将这个算法展开成网络，之前假设已知的那些矩阵转化为了 1x1 卷积，proximal 算子则是一个残差网络。通过网络的学习获得这些参数。

hyperspectral 问题的描述：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\boldsymbol{Y}=\boldsymbol{X} \boldsymbol{R}+\boldsymbol{N}_{y}} \\ {\boldsymbol{Z}=\boldsymbol{C} \boldsymbol{X}+\boldsymbol{N}_{z}}\end{array}" border="0"/>

X 是未知的图像，Y 是高清但是 band 有限的，Z 的band 是完整的，但是低清。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200218195913.png" width="70%"/>

为了建立重建算法，作者先考虑了这样一个信号分解：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{X}=\boldsymbol{Y} \boldsymbol{A}+\hat{\boldsymbol{Y}} \boldsymbol{B}" border="0"/>

这个分解也可以用下图看出：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200218200203.png" width="70%"/>

于是，作者构建这样一个重建问题：

<img src="http://latex.codecogs.com/svg.latex? \min _{\hat{Y}}\|\boldsymbol{C}(\boldsymbol{Y} \boldsymbol{A}+\hat{\boldsymbol{Y}} \boldsymbol{B})-\boldsymbol{Z}\|_{F}^{2}+\lambda f(\hat{\boldsymbol{Y}})" border="0"/>

注意：这里只估计 $\hat{Y}$，A，B 都当作已知的。迭代算法和展开的网络设计如下图所示。proximal 算子哈爱上有一个网络来表示了。文章中说用的是一个残差网络。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200218200405.png" width="80%"/>

这里的 A 其实只是 s x S 的矩阵，B 是 (r-s)x S 的矩阵，所以实际上只是 1x1 卷积。C 是已知的，对应的是下采样。对应的网络结构设计：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200218200424.png"/>

这里，因为一开始假设 $\hat{Y}$ 是 0，所以输入部分和其它 block 不一样。另外作者在最终的输出 $\hat{X}^{(K)}$ 后面加了一个 resNet。作者说因为有噪声，groundtruth 可能不是重建的结果，所以加了一个网络。损失函数是包含 $\epsilon$ 的，因为它表示了 Z 的残差。

<img src="http://latex.codecogs.com/svg.latex? L=\|\hat{\mathcal{X}}-\mathcal{X}\|_{F}^{2}+\alpha \sum_{k=1}^{K}\left\|\mathcal{X}^{(k)}-\mathcal{X}\right\|_{F}^{2}+\beta\left\|\mathcal{E}^{(K)}\right\|_{F}^{2}" border="0"/>

### Bibtex:

```latex
@inproceedings{xie2019multispectral,
  title={Multispectral and hyperspectral image fusion by MS/HS fusion net},
  author={Xie, Qi and Zhou, Minghao and Zhao, Qian and Meng, Deyu and Zuo, Wangmeng and Xu, Zongben},
  booktitle={Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition},
  pages={1585--1594},
  year={2019}
}
```

