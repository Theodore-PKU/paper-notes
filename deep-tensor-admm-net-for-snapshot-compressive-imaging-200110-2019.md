### Paper:

Deep Tensor ADMM-Net for Snapshot Compressive Imaging

### Author:

Jiawei Ma, Xiao-Yang Liu, Zheng Shou, Xin Yuan

### Year:

2019

### Notes:

阅读日期：2010.01.10

泛读。这篇文章解决的问题是 Snapshot Compressive Imaging，和多帧视频的重建有关系。方法基本和 ADMM-net 一样，但是由于处理的数据是3D的，所以在细节有很多不同。首先作者考虑的是经过变换后的核范数作为正则项。另外在ADMM框架中，涉及到计算矩阵逆的部分，作者利用了矩阵的RBD结构，使用一种快速计算的方法来实现。由于方法和ADMM-net一样，所以泛读。

问题的数学描述：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{Y}=\sum_{b=1}^{B} \mathcal{C}^{(b)} \odot \mathcal{X}^{(b)}+\boldsymbol{N}"  border="0"/>

Y 是测量值，B 表示帧数，X 是真实的B帧图像，C表示采样矩阵（一个mask，每一帧都不同），N为噪声。可以把上式改写成线性系统的形式。

一个数学概念，张量的核范数。把张量的每个分量矩阵按照对角的形式排列，其核范数式定义为张量的核范数
$$
\overline{\mathcal{T}} \triangleq\left[\begin{array}{cccc}{\widetilde{\mathcal{T}}^{(1)}} & {} & {} \\ {} & {\widetilde{\mathcal{T}}^{(2)}} & {} \\ {} & {} & {\ddots} & {} \\ {} & {} & {} & {\widetilde{\mathcal{T}}^{(B)}} \end{array}\right] \in \mathbb{C}^{n_{1} B \times n_{2} B}
$$
模型描述：
$$
\underset{\mathcal{X} \in \mathbb{R}^{n}_{1} \times n_{2} \times B}{\operatorname{argmin}} \sum_{f=1}^{F} w_{f}\|\mathcal{X}\|_{\Lambda_{f}, \operatorname{TNN}}
$$
<img src="http://latex.codecogs.com/svg.latex? \text { s.t. } \boldsymbol{y}=\boldsymbol{\Phi} \boldsymbol{x}+\boldsymbol{n}"/>

这里的 <img src="http://latex.codecogs.com/svg.latex? \Lambda_{f}"> 指的是某一个变换，核范数是在变换后计算的。这些变换对应于 网络结构中的 Domain transform （FC layer）。

在ADMM算法中，涉及到一个矩阵的求逆。由于SCI的问题特性，其矩阵的结构是Rectangular Diagonal Block (RDB)，可以有特殊的快速求解算法。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-10%E4%B8%8A%E5%8D%888.16.38.png" width="50%"/>

核范数对应的 shrinkage 函数是 Singular value shrinkage operator。有以下定义和结论：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-10%E4%B8%8A%E5%8D%889.09.13.png" width="50%"/>

展开后的网络结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-10%E4%B8%8A%E5%8D%888.16.28.png" width="90%">

### Bibtex:

```latex
@inproceedings{ma2019deep,
  title={Deep tensor admm-net for snapshot compressive imaging},
  author={Ma, Jiawei and Liu, Xiao-Yang and Shou, Zheng and Yuan, Xin},
  booktitle={Proceedings of the IEEE International Conference on Computer Vision},
  pages={10223--10232},
  year={2019}
}
```