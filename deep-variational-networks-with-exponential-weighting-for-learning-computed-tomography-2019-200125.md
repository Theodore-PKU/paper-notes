### Paper:

Deep Variational Networks with Exponential Weighting for Learning Computed Tomography

### Author:

Valery Vishnevskiy, Richard Rau, and Orcun Goksel

### Year:

2019

### Notes:

阅读时间：2020.01.25

泛读。这篇文章研究的是 Computed Tomography，主要实验是 CT，但也可以用于其他类似的成像系统。作者的想法是 variational networks，在论文中，作者对于变分网络的描述是 unroll 形式的network。这篇文章也是这样，不过作者没有推导他的网络结构对应的公式如何，也没有网络的示意图。关于损失函数，作者使用了一个带指数权重的损失函数，来调节训练是不同中间结果的参与度。

基本的重建公式为：

<img src="http://latex.codecogs.com/svg.latex? \hat{\mathbf{x}}(\mathbf{b} ; \lambda, p)=\underset{\mathbf{x}}{\operatorname{argmin}}\|\mathbf{L} \mathbf{x}-\mathbf{b}\|_{p}^{p}+\lambda \mathcal{R}(\mathbf{x})" border="0"/>

使用的网络结构的数学表达为：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \mathbf{g}^{(k)} &\leftarrow\left(\mathbf{P}^{(k)} \mathbf{L} \mathbf{Q}^{(k)}\right)^{\top} \mathbf{W}_{d}^{(k)} \varphi_{d}^{(k)}\left\{\mathbf{W}_{d}^{(k)}\left(\mathbf{P}^{(k)} \mathbf{L} \mathbf{Q}^{(k-1)}-\mathbf{b}\right)\right\}+\\ &\left(\mathbf{D}^{(k)}\right)^{\top} \mathbf{W}_{r}^{(k)} \varphi_{r}^{(k)}\left\{\mathbf{W}_{r}^{(k)} \mathbf{D}^{(k)} \mathbf{x}^{(k-1)}\right\} \\ \mathbf{s}^{(k)} &\leftarrow \alpha^{(k)} \mathbf{s}^{(k-1)}+\mathbf{g}^{(k)},  \mathbf{x}^{(k)} \leftarrow \mathbf{x}^{(k-1)}-\mathbf{s}^{(k)} \end{aligned}" border="0"/>

$\mathbf{P}^{(k)},\mathbf{Q}^{(k)},\mathbf{D}^{(k)}$ 是卷积，$\mathbf{W}_d^{(k)}, \mathbf{W}_r^{(k)}$ 是 diagonal 权重，$\varphi_d^{(k)}, \varphi_r^{(k)}$ 是激活函数，使用 linear interpolation on a regular grid 参数化（但是不懂具体是什么）。卷积参数作者使用了 zero-centered unit-norm。$\mathbf{g}^{(k)}$ 表示的含义是 gradient，$\mathbf{s}^{(k)}$ 表示的是 moment。网络的初始输入为 $\mathbf{L}^{\top} \mathbf{b}$.

损失函数为：

<img src="http://latex.codecogs.com/svg.latex? \min _{\Theta} \underset{\left\{\mathbf{b}, \mathbf{x}^{*}\right\} \in \mathcal{T}}{\mathbb{E}} \sum_{k=1}^{K} e^{-\tau(K-k)}\left\|\mathbf{x}^{(k)}(\mathbf{b} ; \Theta)-\mathbf{x}^{\star}\right\|_{1}" border="0"/>

注意这里的 k 表示了迭代的中间过程。损失函数中的 $\tau$，可以控制训练时中间模块的输出是否参与到损失函数的计算中，当 $ \tau$ 为无穷大时，只对最后的输出计算损失函数。作者采用的训练策略是逐渐增大 $\tau$。作者认为，直觉上，这样的做法可以训练得比较快。

### Bibtex:

```latex
@inproceedings{vishnevskiy2019deep,
  title={Deep variational networks with exponential weighting for learning computed tomography},
  author={Vishnevskiy, Valery and Rau, Richard and Goksel, Orcun},
  booktitle={International Conference on Medical Image Computing and Computer-Assisted Intervention},
  pages={310--318},
  year={2019},
  organization={Springer}
}
```

