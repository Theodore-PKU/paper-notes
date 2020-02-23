### Paper:

Extra Proximal-Gradient Inspired Non-local Network

### Author:

Qingchao Zhang, Yunmei Chen

### Year:

2019

### Notes:

阅读日期：2020.02.18

泛读。这篇文章研究的是展开一个 extra gradient 算法的网络结构，并且作者还加了一层 non-local 的操作。extra gradient 指的是相对于 ISTA 算法，有两次 gradient 和 proximal 计算。展开成网络后，proximal 的网络结构作者直接设计成 卷积 + thresholding + non-local + 卷积。thresholding 的目的是增加 coding 部分的稀疏性。基本上所有的参数都可学习，并且不共享。non-local 部分和一般的处理方式都差不多，不过权重系数的标准化用的是 softmax。

设原始目标函数

<img src="http://latex.codecogs.com/svg.latex? \min _{\mathbf{x}} f(\mathbf{x})+g(\mathbf{x})" border="0"/>

extra gradient 算法：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \mathbf{x}^{k+\frac{1}{2}} &=\operatorname{Prox}_{\alpha_{k}, g}\left(\mathbf{x}^{k}-\alpha_{k} \nabla f\left(\mathbf{x}^{k}\right)\right) \\ \mathbf{x}^{k+1} &=\operatorname{Prox}_{\beta_{k}, g}\left(\mathbf{x}^{k}-\beta_{k} \nabla f\left(\mathbf{x}^{k+\frac{1}{2}}\right)\right) \end{aligned}" border="0"/>

作者进一步利用 Nesterov’s accelerated gradient method，所以实际展开的算法是：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \widetilde{\mathbf{x}}^{k} &=\mathbf{x}^{k}+\gamma_{k}\left(\mathbf{x}^{k}-\mathbf{x}^{k-\frac{1}{2}}\right) \\ \mathbf{b}^{k+\frac{1}{2}} &=\widetilde{\mathbf{x}}^{k}-\alpha_{k} \nabla f\left(\widetilde{\mathbf{x}}^{k}\right) \\ \mathbf{x}^{k+\frac{1}{2}} &=\operatorname{Prox}_{\lambda \alpha_{k},\left\|\mathcal{G}_{k}\right\|_{1}}\left(\mathbf{b}^{k+\frac{1}{2}}\right) \\ \widehat{\mathbf{x}}^{k} &=\mathbf{x}^{k+\frac{1}{2}}+\gamma_{k}\left(\mathbf{x}^{k+\frac{1}{2}}-\mathbf{x}^{k}\right) \\ \mathbf{b}^{k+1} &=\widehat{\mathbf{x}}^{k}-\beta_{k} \nabla f\left(\widehat{\mathbf{x}}^{k}\right) \\ \mathbf{x}^{k+1} &=\operatorname{Prox}_{\lambda \beta_{k},\left\|\mathcal{G}_{k}\right\|_{1}}\left(\mathbf{b}^{k+1}\right) \end{aligned}" border="0"/>

把 proximal 算子的部分设计成如下的形式：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \mathbf{x}^{k+\frac{1}{2}} &=\mathbf{b}^{k+\frac{1}{2}}+\widetilde{\mathcal{G}}_{k} \circ \mathcal{N}_{k}\left(\operatorname{soft}\left(\mathcal{G}_{k}\left(\mathbf{b}^{k+\frac{1}{2}}\right), \boldsymbol{\theta}_{k}\right)\right) \\ \mathbf{x}^{k+1} &=\mathbf{b}^{k+1}+\widetilde{\mathcal{G}}_{k} \circ \mathcal{N}_{k}\left(\operatorname{soft}\left(\mathcal{G}_{k}\left(\mathbf{b}^{k+1}\right), \boldsymbol{\theta}_{k}\right)\right) \end{aligned}" border="0"/>

$\mathcal{G}_k$ 的结构是 $\operatorname{BReLU}(\mathbf{A D X})$，$\widetilde{\mathcal{G}}_k$ 的结构是 $\widetilde{\mathbf{D}} \widetilde{\mathbf{A}} \operatorname{Re} L U(\widetilde{\mathbf{B}} \mathbf{x})$，soft 表示 soft-thresholding。$\mathcal{N}_k$ 则表示 non-local 操作，表达式可以写成：

<img src="http://latex.cod ecogs.com/svg.latex? \mathcal{N}_{k}\left(\overline{\mathbf{x}}^{k}\right)=\operatorname{Re} L U\left(\mathcal{C}_{k}\left[\mathbf{z}^{k}, \mathcal{S}\left(\left(\mathbf{W}_{\alpha}^{k} \mathbf{z}^{k}\right)^{\mathrm{T}} \mathbf{W}_{\beta}^{k} \mathbf{z}^{k}\right) \mathbf{W}_{\varphi}^{k} \mathbf{z}^{k}\right]\right)" border="0"/>

基本上都是卷积操作。没有什么特别的地方。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200218151902.png"/>

non-local 的结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200218151920.png" width="70%"/>

损失函数是 2 范数。实验用的是和 ISTA 算法一样的数据集，都是 patch 训练，任务是压缩感知重建。

### Bibtex:

```latex
@article{zhang2019extra,
  title={Extra Proximal-Gradient Inspired Non-local Network},
  author={Zhang, Qingchao and Chen, Yunmei},
  journal={arXiv preprint arXiv:1911.07144},
  year={2019}
}
```

