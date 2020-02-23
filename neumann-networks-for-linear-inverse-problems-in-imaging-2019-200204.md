### Paper:

Neumann Networks for Linear Inverse Problems in Imaging

### Author:

Davis Gilton, Greg Ongie, Rebecca Willett

### Year:

2019

### Notes:

阅读时间：2020.02.04

泛读。这篇文章研究的是线性逆问题的图像重建。提出的方法本质还是 unroll 一个算法，不过，这篇文章对于正则项的假设不太一样。作者先假设了一个线性的正则项，展开后用线性代数的矩阵处理技巧改变形式，最终推出的迭代形式有一个中间值求和，因此在网络结构中增加了一个额外的 skip connection。总的来说，这篇文章没有什么特别的地方。

<img src="http://latex.codecogs.com/svg.latex? \widehat{\boldsymbol{\beta}}=\underset{\boldsymbol{\beta}}{\arg \min } \frac{1}{2}\|\boldsymbol{y}-\boldsymbol{X} \boldsymbol{\beta}\|_{2}^{2}+\frac{1}{2} \boldsymbol{\beta}^{\top} {\boldsymbol{R}} \boldsymbol{\beta}" border="0"/>

极小值写为：

<img src="http://latex.codecogs.com/svg.latex? \left(\boldsymbol{X}^{\top} \boldsymbol{X}+\boldsymbol{R}\right) \boldsymbol{\beta}^{*}=\boldsymbol{X}^{\top} \boldsymbol{y}" border="0"/>

利用 Neumann series

<img src="http://latex.codecogs.com/svg.latex? \(\boldsymbol{I}-\boldsymbol{A})^{-1}=\sum_{k=0}^{\infty} \boldsymbol{A}^{k}=\boldsymbol{I}+\boldsymbol{A}+\boldsymbol{A}^{2}+\boldsymbol{A}^{3} \cdots" border="0"/>

极小值的解可以写成：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{\beta}^{\star}=\sum_{j=0}^{\infty}\left(\boldsymbol{I}-\eta \boldsymbol{X}^{\top} \boldsymbol{X}-\eta \boldsymbol{R}\right)^{j}\left(\eta \boldsymbol{X}^{\top} \boldsymbol{y}\right)" border="0"/>

如果只取前 B 项，得到

<img src="http://latex.codecogs.com/svg.latex? \widehat{\boldsymbol{\beta}}(\boldsymbol{y}):=\sum_{j=0}^{B}\left(\left[\boldsymbol{I}-\eta \boldsymbol{X}^{\top} \boldsymbol{X}\right](\cdot)-\eta R(\cdot)\right)^{j}\left(\eta \boldsymbol{X}^{\top} \boldsymbol{y}\right)" border="0"/>

这时可以用神经网络来替代 $R(\cdot)$，得到的网络示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-04%E4%B8%8A%E5%8D%8811.29.19.png"/>

另外，作者还利用下式：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{T}_{\lambda}:=\left(\boldsymbol{X}^{\top} \boldsymbol{X}+\lambda \boldsymbol{I}\right)^{-1}" border="0"/>

对这个推导进行了重新整理，得到另一种写法：

<img src="http://latex.codecogs.com/svg.latex? \widehat{\boldsymbol{\beta}}_{p c}(\boldsymbol{y})=\sum_{j=0}^{B}\left(\lambda \boldsymbol{T}_{\lambda}(\cdot)-\tilde{R}(\cdot)\right)^{j} \boldsymbol{T}_{\lambda} \boldsymbol{X}^{\top} \boldsymbol{y}" border="0"/>

网络模块的结构比较简单，是一个 7 层的卷积网络。

### Bibtex:

```latex
@article{gilton2019neumann,
  title={Neumann Networks for Linear Inverse Problems in Imaging},
  author={Gilton, Davis and Ongie, Greg and Willett, Rebecca},
  journal={IEEE Transactions on Computational Imaging},
  year={2019},
  publisher={IEEE}
}
```

