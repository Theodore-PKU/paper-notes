### Paper:

Dynamic MRI using model‐based deep learning and SToRM priors: MoDL‐SToRM

### Author:

Sampurna Biswas, Hemant K. Aggarwal, Mathews Jacob

### Year:

2019

### Notes:

阅读时间：2020.02.02

泛读。这篇文章研究的是动态 MRI 重建。动态 MRI 重建时容易收到呼吸的影响，所以和一般的静态MRI 重建有一些不同。这篇文章的作者是 MoDL: Model-Based Deep Learning Architecture for Inverse Problems 的作者，这篇文章的思路也是 MoDL，只不过因为动态 MRI，增加了一个新的和网络无关的正则项 SToRM。整体的思路还是先给出一个带网络和 SToRM 正则项的目标函数，然后写出 alternative 算法。由于使用了 SToRM，所以计算量很大，为此，作者在训练方式上使用了一个技巧。另外，实际训练时，作者先训练一个去噪网络 $\mathcal{D}_w$，然后训练 unfold 一次迭代的网络，最后把该层网络的参数作为多次迭代的各个部分的网络参数初始化，进行进一步训练。

目标函数：

<img src="http://latex.codecogs.com/svg.latex? \mathcal{C}(\mathbf{X}) =\underbrace{\|\mathcal{A}(\mathbf{X})-\mathbf{B}\|_{2}^{2}}_{\text{data consistency }}+\underbrace{\|\mathcal{N}_{\mathbf{w}}(\mathbf{X})\|^{2}}_{\text {CNN prior}} +\frac{\lambda_{2}}{2} \underbrace{\operatorname{tr}\left(\mathbf{X}^{T} \mathbf{L} \mathbf{X}\right)}_{\text {SToRM prior }} " border="0"/>

其中 $\mathbf{L} = \mathbf{D} - \mathbf{W}$，$\mathbf{D}$ 是对角矩阵，$\mathbf{D}_{(i, i)}=\sum_{j} \mathbf{W}_{(i, j)}$，$\mathbf{W}_{(i, j)}$ 表示 $x_i,x_j$ 之间的相似程度。$\mathbf{W}$ 是事先算好的。网络的输入则是使用 SToRM 算法的结果，即求解下式：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{X}_{0}=\arg \min _{\mathbf{X}}\|\mathcal{A}(\mathbf{X})-\mathbf{B}\|_{2}^{2}+\frac{\eta}{2} \operatorname{tr}\left(\mathbf{X}^{T} \mathbf{L} \mathbf{X}\right)" border="0"/>

整体的计算过程为：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{c}{\mathbf{Y}_{n}=\mathcal{D}_{\mathbf{w}}\left(\mathbf{X}_{n}\right)} \\ {\mathbf{Q}_{n}=\mathbf{W} \mathbf{X}_{n}} \\ {\mathbf{R}_{n}=\left(\mathcal{A}^{*}(\mathbf{B})+\lambda_{1} \mathbf{Y}_{n}+\lambda_{2} \mathbf{Q}_{n}\right)} \\ {\mathbf{X}_{n+1}=\left(\mathcal{A}^{*} \mathcal{A}+\lambda_{1} \mathbf{I}+\lambda_{2} \mathbf{D}\right)^{-1} \mathbf{R}_{n}}\end{array}" border="0"/>

网络结构部分，比较简单，没有什么特别的地方，因为使用的还是 MoDL 的方法，示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-02%E4%B8%8B%E5%8D%886.19.492.png"/>

因为计算量较大，所以作者在训练时，先固定网络参数，前向计算出所有的 $Q_n$ 后，将 $Q_n$ 保存下来，再进行梯度计算。如图所示：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-02%E4%B8%8B%E5%8D%886.22.08.png"/>

### Bibtex:

```latex
@article{biswas2019dynamic,
  title={Dynamic MRI using model-based deep learning and SToRM priors: MoDL-SToRM},
  author={Biswas, Sampurna and Aggarwal, Hemant K and Jacob, Mathews},
  journal={Magnetic resonance in medicine},
  volume={82},
  number={1},
  pages={485--494},
  year={2019},
  publisher={Wiley Online Library}
}
```

