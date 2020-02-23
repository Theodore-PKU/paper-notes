### Paper:

Learning Cluster Structured Sparsity By Reweighting

### Author:

Yulun Jiang, Lei Yu, Haijian Zhang and Zhou Liu

### Year:

2019

### Notes:

阅读日期：2020.02.14

泛读。这篇文章研究的是稀疏信号重建的问题，并不是具体的某一个逆问题，主要的关注点是如何更好地利用信号域的稀疏性。因此作者的主要创新点集中在稀疏性的罚项上。稀疏性的 1 范数罚项，一般有个系数，但是对所有的 support 集上的分量都是一致的，有人提出了带权重的罚项。作者顺着这个思路，将一个传统算法 RwISTA 改成一个深度网络的形式，可学习的参数集中在权重的表示上。最开始的表示比较简单，是一个带参数的逆 sigmoid 函数，之后改成全连接层，表示全局特征下的权重计算。实验表明，权重计算的大部分集中在对角线区域，也就是说像素的邻域是主要影响的来源，作者进而使用一个卷积层来表示权重的计算。换言之，最后网络部分就是存在于权重计算。算法的其它部分没有变化。

作者假设的模型直接是：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{y}=\boldsymbol{A} \boldsymbol{x}+\epsilon" border="0"/>

$\boldsymbol{x}$ 本身就是稀疏的。稀疏性有很多种假设，一种是 structured sparsity。

sparsity 的结构可以用权重控制。

<img src="http://latex.codecogs.com/svg.latex? \min _{\boldsymbol{x} \in \mathbb{R}^{N}}\|\boldsymbol{y}-\boldsymbol{A} \boldsymbol{x}\|_{2}^{2}+\lambda \sum_{i=1}^{N} w_{i}\left|x_{i}\right|" border="0"/>

类似于 ISTA 算法的迭代：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{w}^{(k)} \leftarrow \varphi\left(\left|\boldsymbol{x}^{(k)}\right|\right), \boldsymbol{x}^{(k+1)} \leftarrow \Gamma\left(\boldsymbol{x}^{(k)}+\frac{1}{L} \boldsymbol{A}^{T}(\boldsymbol{y}-\boldsymbol{A}) \boldsymbol{x}^{(k)}, \frac{\lambda}{L} \boldsymbol{w}^{(k)}\right)" border="0"/>

这里的 $\Gamma$ 是 soft-threshoding，$\varphi$ 是计算权重的函数，作者的主要关注点就在这里。

一种传统的做法是 $[\varphi(\boldsymbol{x})]_{i}=g^{\prime}\left(x_{i}\right), i=1,2, \ldots, N$，$g(x)$ 是一个非递减函数，一种常见的取法是对数函数。

作者提出的 RW-LISTA

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{w}^{(k)} \leftarrow \varphi^{(k)}\left(\left|\boldsymbol{x}^{(k)}\right|\right), \boldsymbol{x}^{(k+1)} \leftarrow \Gamma\left(\boldsymbol{B}^{(k)} \boldsymbol{x}^{(k)}+\boldsymbol{C}^{(k)} \boldsymbol{y}, \theta^{(k)} \boldsymbol{w}^{(k)}\right)" border="0"/>

这里虽然看起来有很多参数，但实际上只有 $\varphi$ 的参数是可学习的。下面给出三种形式：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{w}^{(k)} \leftarrow \sigma\left(-t^{(k)}\left|\boldsymbol{x}^{(k)}\right|\right)" border="0"/>

$\sigma$ 是 $\sigma(-x)=\dfrac{1}{1+e^{x}}$ 

第二种的 $G$ 是一个全连接矩阵。

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{w}^{(k)} \leftarrow \sigma\left(-\boldsymbol{G}^{(k)}\left|\boldsymbol{x}^{(k)}\right|\right)" border="0"/>

第三种是卷积运算，这里是两个卷积层。

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{w}^{(k)} \leftarrow \sigma\left(-\boldsymbol{v}_{2}^{(k)} \otimes \operatorname{ReLU}\left(\boldsymbol{v}_{1}^{(k)} \otimes\left|\boldsymbol{x}^{(k)}\right|\right)\right)" border="0"/>

### Bibtex:

```latex
@article{jiang2019learning,
  title={Learning Cluster Structured Sparsity by Reweighting},
  author={Jiang, Yulun and Yu, Lei and Zhang, Haijian and Liu, Zhou},
  journal={arXiv preprint arXiv:1910.05303},
  year={2019}
}
```

