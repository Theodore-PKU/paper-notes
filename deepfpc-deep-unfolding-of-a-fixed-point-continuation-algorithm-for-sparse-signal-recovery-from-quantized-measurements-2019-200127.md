### Paper:

DeepFPC: Deep Unfolding of a Fixed-Point Continuation Algorithm for Sparse Signal Recovery from Quantized Measurements

### Author:

Peng Xiao, Bin Liao, Nikos Deligiannis

### Year:

2019

### Notes:

阅读时间：2020.01.27

泛读。这篇文章研究的是 1 bits 的压缩感知信号重建，使用的方法是将 FPC 算法展开成网络的形式。这里作者直接把原来算法中的矩阵变成了可学习的矩阵，为了方便求导，把 sign 函数用 tanh 函数替换。

1 bits 问题可以写为：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{y}=\operatorname{sign}(\mathbf{\Phi} \mathbf{x}+\varepsilon)" border="0"/>

FPC 算法的目标函数是：

<img src="http://latex.codecogs.com/svg.latex? \min _{\mathbf{x}}\|\mathbf{x}\|_{1}+\lambda \sum_{i=1}^{M} h\left([\mathbf{Y} \mathbf{\Phi} \mathbf{x}]_{i}\right) \text { s.t. }\|\mathbf{x}\|_{2}=1" border="0"/>

其中 $h(z) = \max\{0,-z\}$ 表示 one-side $l_1$ norm，$h(z) = (\max\{0,-z\})^2$ 表示 one-side $l_2$ norm，当选择前者时，FPC 算法的具体过程为：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\mathbf{u}=S_{\nu}\left(\mathbf{x}^{(r)}-\tau \mathbf{g}\left(\mathbf{x}^{(r)}\right)\right)} \\ {\mathbf{x}^{(r+1)}=\mathbf{u} /\|\mathbf{u}\|_{2}}\end{array}" border="0"/>

其中 $S_{\nu}(\cdot)$ 是 soft-thresholding 函数，$\mathbf{g}(\mathbf{x})=\mathbf{\Phi}^{T}(\operatorname{sign}(\mathbf{\Phi} \mathbf{x})-\mathbf{y})$.

作者把第一步替换成：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{u}=S_{\nu}\left(\mathbf{x}^{(r)}+\mathbf{C} \operatorname{sign}\left(\mathbf{B} \mathbf{x}^{(r)}\right)+\mathbf{A} \mathbf{y}\right)" border="0"/>

$\text{sign}$ 函数用 $\tanh (\kappa u)$ 替换，A，B，C 都是可学习的矩阵参数。整体 unfold 之后的网络结构又采取了 residual 的形式。网络结构示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-28%E4%B8%8A%E5%8D%8810.15.36.png" width="50%"/>

注意，本来 FPC 算法中对 $x$ 有范数的约束，在网络中，只有最后一层是用了这个约束。另外，在训练时 $\kappa$ 的值是从小逐渐增大。因为较小的 $\kappa$ 值不易出现梯度消失，但较大的 $\kappa$ 值会让 tanh 函数更逼近 sign 函数。

### Bibtex:

```latex
@article{xiao2019deepfpc,
  title={DeepFPC: Deep Unfolding of a Fixed-Point Continuation Algorithm for Sparse Signal Recovery from Quantized Measurements},
  author={Xiao, Peng and Liao, Bin and Deligiannis, Nikos},
  journal={arXiv preprint arXiv:1912.00838},
  year={2019}
}
```

