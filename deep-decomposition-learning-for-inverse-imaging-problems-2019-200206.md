### Paper:

Deep Decomposition Learning for Inverse Imaging Problems

### Author:

Dongdong Chen, Mike E. Davies

### Year:

2019

### Notes:

阅读时间：2020.02.06

精读。这篇文章研究的是图像逆问题，作者思考的视角是如何利用测量矩阵和测量噪声。基本的想法是将重建的信号分解为两个相互正交的信号，一个是测量矩阵的零空间，一个是测量矩阵伪逆张成的空间，这两个信号分别用两个网络来学习和表示。作者提出了两种结构来整合这两个网络并重建出信号，一种是相互独立，一种是 cascade。相应的，损失函数也针对这两个网络进行了一定的设计。作者之所以要这样处理，最主要的目的是为了更好地去除测量值的噪声带来的问题。

假设信号的模型为：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{y}_{\epsilon}=\mathbf{H} \mathbf{x}+\epsilon" border="0"/>

任何一个信号都可以分解为：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{x}=\mathcal{P}_{r}(\boldsymbol{x})+\mathcal{P}_{n}(\boldsymbol{x})" border="0"/>

其中，$\mathcal{P}_{r} \triangleq \boldsymbol{H}^{\dagger} \boldsymbol{H},\mathcal{P}_{n} \triangleq\left(\boldsymbol{I}_{D}-\boldsymbol{H}^{\dagger} \boldsymbol{H}\right)$，这两个变换分别对应信号空间的分解，$\mathbb{R}^{D}=\mathcal{R}\left(\mathbf{H}^{\dagger}\right) \oplus \mathcal{N}(\mathbf{H})$. 于是，在有噪声的情况下（改写为 $\mathbf{y}_{\epsilon}=\mathbf{H x}-\epsilon$），可以得到如下的分解：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{x}=\mathcal{P}_{r}(\mathbf{x})+\mathcal{P}_{n}(\mathbf{x}) =\mathbf{H}^{\dagger} \mathbf{y}_{\epsilon}+\mathbf{H}^{\dagger} \epsilon+\mathcal{P}_{n}(\mathbf{x})" border="0"/>

如果 $\epsilon$ 为 0，则 $\mathbf{H}^{\dagger} \mathbf{y}_{\epsilon}$ 就是 $\mathcal{P}_r(x)$，但是因为 $\epsilon$ 不为 0，这个误差对一般的方法会有一定的影响。注意到 $\mathbf{H}^{\dagger} \epsilon$ 其实也属于 $\mathcal{R}\left(\mathbf{H}^{\dagger}\right)$。作者设想了两种模式来重建信号，如下图所示：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-06%E4%B8%8B%E5%8D%882.46.06.png"/>

从直觉上说，后者比前者更好，因为后者在重建 $P_n$ 部分的时候，用的是没有噪声的 $\mathcal{P}_r(x)$，作者的实验也表明，后者效果更好。分别设两个网络为 $\mathcal{F},\mathcal{G}$，（具体的结构分别是一个 CNN 和 U-Net）可以得到如下的两种网络表达式（注意，这里复合了 $\mathcal{P}_r$ 和 $\mathcal{P}_n$，猜测作用是为了保证信号分解。）：

<img src="http://latex.codecogs.com/svg.latex? \mathcal{A}_{i}\left(\mathbf{y}_{\epsilon}\right) \triangleq \mathbf{H}^{\dagger} \mathbf{y}_{\epsilon}+\mathcal{P}_{r}\left(\mathcal{F}\left(\mathbf{H}^{\dagger} \mathbf{y}_{\epsilon}\right)\right)+\mathcal{P}_{n}\left(\mathcal{G}\left(\mathbf{H}^{\dagger} \mathbf{y}_{\epsilon}\right)\right)" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \mathcal{A}_{c}\left(\mathbf{y}_{\epsilon}\right)  \triangleq \mathbf{H}^{\dagger} \mathbf{y}_{\epsilon}+\mathcal{P}_{r}\left(\mathcal{F}\left(\mathbf{H}^{\dagger} \mathbf{y}_{\epsilon}\right)\right) +\mathcal{P}_{n}\left(\mathcal{G}\left(\mathbf{H}^{\dagger} \mathbf{y}_{\epsilon}+\mathcal{P}_{r}\left(\mathcal{F}\left(\mathbf{H}^{\dagger} \mathbf{y}_{\epsilon}\right)\right)\right)\right)" border="0"/>

最后是关于损失函数的介绍。有两种训练方式，一种是一起训练，一种是分开训练。

对于一起训练，包含三个损失函数：

<img src="http://latex.codecogs.com/svg.latex? \ell_{\mathrm{emp}}(\mathcal{A}) \triangleq \frac{1}{N} \sum_{\mathbf{y}_{\epsilon}^{(i)}, \mathbf{x}^{(i)} \in \mathbf{X}} \ell\left(\mathcal{A}\left(\mathbf{y}_{\epsilon}^{(i)}\right), \mathbf{x}^{(i)}\right)" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \phi_{1}(\mathcal{F})=\sum_{i=1}^{N} \ell\left(\mathbf{H} \mathcal{F}\left(\mathbf{H}^{\dagger} \mathbf{y}_{\epsilon}^{(i)}\right), \epsilon^{(i)}\right)" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \phi_{2}(\mathcal{G})=\sum_{j}\left\|\mathbf{w}^{(j)}\right\|_{2}^{2}" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \min _{\mathcal{F}, \mathcal{G}} \ell_{\mathrm{emp}}(\mathcal{A})+\lambda_{1} \phi_{1}(\mathcal{F})+\lambda_{2} \phi_{2}(\mathcal{G})" border="0"/>

分析：第二项是保证 $\mathcal{F}$ 学到的是误差，第三项只是网络参数的正则项，因为 $l_{emp}$ 已经包含了最终结果了，所以不需要有和第二项类似的东西。

对于分开训练，根据网络结构，也不完全相同，对于 independent 模式，可以各自训练：

<img src="http://latex.codecogs.com/svg.latex? \arg \min _{\mathcal{F}}  \frac{1}{N} \sum_{i=1}^{N} \ell\left(\mathbf{H}^{\dagger} \mathbf{y}_{\epsilon}^{(i)}+\mathcal{P}_{r}\left(\mathcal{F}\left(\mathbf{H}^{\dagger} \mathbf{y}_{\epsilon}^{(i)}\right)\right), \mathcal{P}_{r}\left(\mathbf{x}^{(i)}\right)\right) +\lambda_{1} \phi_{1}(\mathcal{F}) " border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \arg \min _{\mathcal{G}} \frac{1}{N} \sum_{i=1}^{N} \ell\left(\mathcal{P}_{n}\left(\mathcal{G}\left(\mathbf{H}^{\dagger} \mathbf{y}_{\epsilon}^{(i)}\right)\right), \mathcal{P}_{n}\left(\mathbf{x}^{(i)}\right)\right)+\lambda_{2} \phi_{2}(\mathcal{G})" border="0"/>

对于 cascade 模式，$\mathcal{G}$ 的训练可以是先训练 $\mathcal{F}$ ，然后固定参数，训练：

<img src="http://latex.codecogs.com/svg.latex? \arg \min _{\mathcal{G}} \frac{1}{N} \sum_{i=1}^{N} \ell\left(\mathcal{P}_{n}\left(\mathcal{G}\left(\mathbf{H}^{\dagger} \mathbf{y}_{\epsilon}^{(i)}+\mathcal{P}_{r}\left(\mathcal{F}\left(\mathbf{H}^{\dagger} \mathbf{y}_{\epsilon}^{(i)}\right)\right)\right)\right), \mathcal{P}_{n}\left(\mathbf{x}^{(i)}\right)\right) +\lambda_{2} \phi_{2}(\mathcal{G})" border="0"/>

另外，作者在论文中提到，在计算 $\mathcal{P}_r$ 和 $\mathcal{P}_n$ 时，会涉及到伪逆的计算，如果计算规模较大，可能需要其他方法来估计这个计算。

### Bibtex:

```latex
@article{chen2019deep,
  title={Deep Decomposition Learning for Inverse Imaging Problems},
  author={Chen, Dongdong and Davies, Mike E},
  journal={arXiv preprint arXiv:1911.11028},
  year={2019}
}
```

