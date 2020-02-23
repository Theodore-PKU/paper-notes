### Paper:

On the Convergence of ADMM with Task Adaption and Beyond

### Author:

Risheng Liu, Pan Mu, Jin Zhang

### Year:

2019

### Notes:

阅读日期：2020.02.15

泛读。这篇文章研究的是 ADMM 算法的拓展。现在有很多文章都采用展开 ADMM 算法的方式，用其他的模型输出替代其中的一些运算，作者认为没有足够的收敛保证，为此，作者提出的 ADMM 的拓展，设计了一个有助于收敛的条件，如果不满足该收敛条件，则将迭代的中间步骤的输出值进行调整，使其接近于原始的算法的中间步骤。作者证明了这种做法的收敛性，并且做了一些实验。但是作者没有明确地说明实验中的其它模型（比如 CNN 的具体设计），个人觉得这个实验很不靠谱。

因为考虑的是一般的 ADMM 算法，所以目标函数是一般化的结果：

<img src="http://latex.codecogs.com/svg.latex? \min _{\mathbf{x} \in \mathbb{R}^{n}, \mathbf{y} \in \mathbb{R}^{m}} f(\mathbf{x})+g(\mathbf{y}), \text { s.t. } \mathbf{A} \mathbf{x}+\mathbf{B} \mathbf{y}=\mathbf{c}" border="0"/>

对应的传统的计算：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\mathbf{x}^{k+1}=\arg \min _{\mathbf{x}} \mathcal{L}_{\beta}\left(\mathbf{x}, \mathbf{y}^{k}, \boldsymbol{\lambda}^{k}\right)} \\ {\mathbf{y}^{k+1}=\arg \min _{\mathbf{y}} \mathcal{L}_{\beta}\left(\mathbf{x}^{k+1}, \mathbf{y}, \boldsymbol{\lambda}^{k}\right)} \\ {\boldsymbol{\lambda}^{k+1}=\boldsymbol{\lambda}^{k}-\beta\left(\mathbf{A} \mathbf{x}^{k+1}+\mathbf{B} \mathbf{y}^{k+1}-\mathbf{c}\right)}\end{array}" border="0"/>

事实上，f(x) 常常表示 data fidelity，g(y) 才是正则项。

为了给出最后的算法，作者先讨论了一个求解 x 子问题的变形。设：

<img src="http://latex.codecogs.com/svg.latex? f=l(\mathbf{Q} \mathbf{x})" border="0"/>

可以得到，x 子问题满足：

<img src="http://latex.codecogs.com/svg.latex? \nabla_{\mathbf{x}} \mathcal{L}_{\beta}\left(\mathbf{x}^{k+1}, \mathbf{y}^{k}, \boldsymbol{\lambda}^{k}\right)=\mathbf{Q}^{\top} \nabla l\left(\mathbf{Q} \mathbf{x}^{k+1}\right)+\beta \mathbf{A}^{\top} \mathbf{A} \mathbf{x}^{k+1}-\mathbf{s}_{0}^{k}=0" border="0"/>

设：

<img src="http://latex.codecogs.com/svg.latex? \mathcal{F}_{0}^{k}(\mathbf{x}):=\left(\beta \mathbf{A}^{\top} \mathbf{A}\right)^{-1}\left(\mathbf{s}_{0}^{k}-\mathbf{Q}^{\top} \nabla l(\mathbf{Q} \mathbf{x})\right)" border="0"/>

则，x 的解满足不动点问题：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{x}^{k+1}=\mathcal{F}_{0}^{k}\left(\mathbf{x}^{k+1}\right)" border="0"/>

设误差计算：

<img src="http://latex.codecogs.com/svg.latex? \hat{\mathbf{e}}_{k}(\mathbf{x}):=\nabla l\left(\mathbf{Q} \mathcal{F}_{0}^{k}(\mathbf{x})\right)-\nabla l(\mathbf{Q} \mathbf{x})" border="0"/>

额外的模型为 $\mathcal{D}$，作者给出的收敛条件是：

<img src="http://latex.codecogs.com/svg.latex? \left\|\hat{\mathbf{e}}_{k}\left(\hat{\mathbf{x}}^{k+1}\right)\right\|_{2} \leq \eta\left\|\hat{\mathbf{e}}_{k}\left(\hat{\mathbf{x}}^{k}\right)\right\|_{2}" border="0"/>

总的计算过程可以总结为：

1. 计算 $\check{\mathbf{x}}^{k+1}=\mathcal{D}^{k}\left(\mathbf{x}^{k}\right)	$
2. 判断 $\left\|\mathbf{e}_{k}\left(\check{\mathbf{x}}^{k+1}\right)\right\|_{2} \leq \eta\left\|\mathbf{e}_{k}\left(\hat{\mathbf{x}}^{k}\right)\right\|_{2}$ 是否满足
3. 如果不满足，则用 $\check{\mathbf{x}}^{k+1}=(1-\zeta) \tilde{\mathbf{x}}^{k+1}+\zeta \mathcal{D}^{k}\left(\mathbf{x}^{k}\right)$ （10）来修正，$\tilde{\boldsymbol{x}}^{k+1}$ 是用传统的 x 子问题的求解结果。这一步是为了满足收敛性的一个修正。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200215120009.png" width="70%"/>

### Bibtex:

```latex
@article{liu2019convergence,
  title={On the Convergence of ADMM with Task Adaption and Beyond},
  author={Liu, Risheng and Mu, Pan and Zhang, Jin},
  journal={arXiv preprint arXiv:1909.10819},
  year={2019}
}
```

