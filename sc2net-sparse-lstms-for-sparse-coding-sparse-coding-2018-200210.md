### Paper:

SC2Net: Sparse LSTMs for Sparse Coding: sparse coding

### Author:

Joey Tianyi Zhou, Kai Di, Jiawei Du, Xi Peng, Hao Yang, Sinno Jialin Pan, Ivor W. Tsang, Yong Liu, Zheng Qin, Rick Siow Mong Goh

### Year:

2018

### Notes:

阅读日期：2020.02.10

泛读。研究的是 sparse coding。主要内容是：1. 作者针对 sparse coding 求解算法中，解 coding 部分使用的 ISTA 算法，加了一个 momentum 的中间量，这样可以考虑上一次迭代的结果。2. 这个 momentum 中间量引入了两个向量参数，作为乘积，但是因为考虑 adpative，所以使用网络的方式来计算，即变成类似于 LSTM 的 gate，给定某些输入确定这两个向量，因此整个迭代算法就 cast 为网络结构。可以通过训练来优化出网络的参数。损失函数方面，作者考虑了无监督和有监督两种情形。

原始问题：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{cl}{\min _{\mathbf{S}, \mathbf{B}}} & {\sum_{i}\left\|\mathbf{x}_{i}-\mathbf{B} \mathbf{s}_{i}\right\|_{2}^{2}+\lambda\left\|\mathbf{s}_{i}\right\|_{1}^{2}} \\ {\text { s.t. }} & {\left\|\mathbf{b}_{j}\right\|^{2} \leq 1, \text { and } j=1, \cdots d_{s}}\end{array}" border="0"/>

第一个子问题用的是岭回归的闭式解：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{cl}{\min _{\mathbf{B}}} & {\|\mathbf{X}-\mathbf{B S}\|_{F}^{2}} \\ {\text { s.t. }} & {\left\|\mathbf{b}_{i}\right\|^{2} \leq 1, \text { and } i=1, \cdots d_{s}}\end{array}" border="0"/>

第二个子问题用的是 ISTA 算法：

<img src="http://latex.codecogs.com/svg.latex? \min _{\mathbf{s}} \sum_{i}\left\|\mathbf{x}_{i}-\mathbf{B} \mathbf{s}_{i}\right\|_{F}^{2}+\lambda\left\|\mathbf{s}_{i}\right\|_{1}" border="0"/>

作者提出的 momentum 改进（）$f^{(t)},i^{(t)}$，$W_e,W_d$ 都是已经化简过的结果：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \tilde{\mathbf{c}}^{(t)} &=\mathbf{W}_{e} \mathbf{s}^{(t-1)}+\mathbf{W}_{d} \mathbf{x} \\ \mathbf{c}^{(t)} &=\mathbf{f}^{(t)} \odot \mathbf{c}^{(t-1)}+\mathbf{i}^{(t)} \odot \tilde{\mathbf{c}}^{(t)} \\ \mathbf{s}^{(t)} &=s h_{(\lambda \tau)}\left(\mathbf{c}^{(t)}\right) \end{aligned}" border="0"/>

cast 为网络后：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \mathbf{i}^{(t)} &=\sigma\left(\mathbf{W}_{i s} \mathbf{s}^{(t-1)}+\mathbf{W}_{i x} \mathbf{x}\right) \\ \mathbf{f}^{(t)} &=\sigma\left(\mathbf{W}_{f s} \mathbf{s}^{(t-1)}+\mathbf{W}_{f x} \mathbf{x}\right) \\ \tilde{\mathbf{c}}^{(t)} &=\mathbf{W}_{e} \mathbf{s}^{(t-1)}+\mathbf{W}_{d} \mathbf{x} \\ \mathbf{c}^{(t)} &=\mathbf{f}^{(t)} \odot\left(\mathbf{c}^{(t-1)}+\mathbf{i}^{(t)} \odot \tilde{\mathbf{c}}^{(t)}\right.\\ \mathbf{s}^{(t)} &=h_{(\mathbf{D}, \mathbf{u})}\left(\mathbf{c}^{(t)}\right) \end{aligned}" border="0"/>

网络的迭代结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200210195622.png" width="60%"/>

有监督训练和无监督训练：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200210195638.png"/>

### Bibtex:

```latex
@inproceedings{zhou2018sc2net,
  title={SC2Net: Sparse LSTMs for sparse coding},
  author={Zhou, Joey Tianyi and Di, Kai and Du, Jiawei and Peng, Xi and Yang, Hao and Pan, Sinno Jialin and Tsang, Ivor W and Liu, Yong and Qin, Zheng and Goh, Rick Siow Mong},
  booktitle={Thirty-Second AAAI Conference on Artificial Intelligence},
  year={2018}
}
```

