### Paper:

A Provably Convergent Scheme for Compressive Sensing under Random Generative Priors

### Author:

Wen Huang, Paul Hand, Reinhard Heckel, Vladislav Voroninski

### Year:

2018

### Notes:

泛读。这篇文章可以看作是 [Compressed Sensing using Generative Models](compressed-sensing-using-generative-models.md) 的类似做法的一个收敛性证明。但是从证明的命题来看，我个人觉得非常不可信。作者的主要内容是证明了论文中提出的算法可以保证收敛到一个很好的结果。

假设生成模型 $G:\mathbb{R}^{k} \rightarrow \mathbb{R}^{r}, k \ll n$ 是一个 $d$ 层网络，写为：
$$
G(x)=\operatorname{relu}\left(W_{d} \ldots \operatorname{relu}\left(W_{2} \operatorname{relu}\left(W_{1} x\right)\right) \ldots\right)
$$
$W_i$ 表示矩阵，$\in \mathbb{R}^{n_{i} \times n_{i-1}}$. 问题模型为 $y = A\eta + e$，用生成模型获得 $\eta^* = G(x^*)$，假设测量值 $y$ 已知，通过优化下式得到 $x^*$, 进而得到 $\eta^*$:
$$
\min _{x \in \mathbb{R}^{k}} \frac{1}{2}\|A G(x)-y\|^{2}
$$
文章中，作者给了一个特殊记号来表示relu的作用。
$$
W_{+, x}=\operatorname{diag}(W x>0) W
$$
这样就可以省略 relu，对于 $x$ 直接用一个很多行为 0 的矩阵运算来表示 $\text{relu}(Wx)$. 那么 $G(x)$ 可以写成：
$$
G(x) = \left(\prod_{i=d}^{1} W_{i,+, x}\right) x
$$
作者给出的第一个算法，基于次梯度，也就是一阶梯度算法（后文中，作者提出了一个改进版本的算法，此处略过）：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-02%E4%B8%8B%E5%8D%888.29.40.png" style="zoom: 50%;" />

作者表示，这样的算法有收敛性，在满足两个条件：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-02%E4%B8%8B%E5%8D%888.31.26.png" style="zoom: 33%;" />

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-02%E4%B8%8B%E5%8D%888.31.32.png" style="zoom:33%;" />

收敛性结论为：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-02%E4%B8%8B%E5%8D%888.30.41.png" style="zoom:50%;" />

**但是**，在这两个条件中都没有对 $G(x)$ 有任何实质的要求，也就是满足条件2的参数随机话的生成网络就可以。在这种情况下，即使 $x^*$ 使得 $\|A G(x)-y\|^{2}$ 极小，如何保证 $G(x^*)$ 和 $\eta^*$ 之间的差值不会落入 $A$ 的零空间？由此，放弃继续阅读这篇文章。

### Bibtex:

```latex
@article{huang2018provably,
  title={A provably convergent scheme for compressive sensing under random generative priors},
  author={Huang, Wen and Hand, Paul and Heckel, Reinhard and Voroninski, Vladislav},
  journal={arXiv preprint arXiv:1812.04176},
  year={2018}
}
```