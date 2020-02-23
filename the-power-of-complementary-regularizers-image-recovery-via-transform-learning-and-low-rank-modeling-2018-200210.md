### Paper:

The Power of Complementary Regularizers: Image Recovery via Transform Learning and Low-Rank Modeling

### Author:

Bihan Wen, Yanjun Li, and Yoram Bresler

### Year:

2018

### Notes:

阅读日期：2020.02.10

泛读。这篇文章没有使用深度学习的方法，应该算传统算法，所以算法细节就没有怎么看，因为我觉得没有参考意义。这篇文章研究的是逆问题的图像重建，作者做了去噪、inpainting、MRI-CS 的实验，其主要想法是同时考虑 low-rank 分解和 sparsity，sparsity 是通过 transform learning（并不是字典学习）来限制的。本质就是同时有两个正则项。low-rank 部分的正则项和平常的一样，sparsity 有一些区别，一般的 sparsity 的正则项时真对 patch 的，但是这篇文章针对的是 low-rank 分解中的相似的 patch group，把这个 group 当作 transform 的对象，在数学上，low-rank 是将多个 patch 都先拉直为向量，构成一个矩阵，而在 sparsity 的 group 中，是只选择了 group 的其中 $l$ 个patch（根据论文的说法我是这么理解的），对应 low-rank 中的矩阵的前 $l$ 列，然后再把这个子矩阵拉直为向量，sparisty 是作用在这个向量上的。

**数学形式**

总的目标函数：

<img src="http://latex.codecogs.com/svg.latex? \hat{\boldsymbol{x}}=\underset{\boldsymbol{x}}{\operatorname{argmin}} \gamma^{F}\|\boldsymbol{A} \boldsymbol{x}-\boldsymbol{y}\|_{2}^{2}+\mathfrak{R}_{s t r o l l r}(\boldsymbol{x})" border="0"/>

正则项分成两个部分：

<img src="http://latex.codecogs.com/svg.latex? \mathfrak{R}_{s t r o l l r}(\boldsymbol{x})=\gamma^{L R} \mathfrak{R}_{L R}(\boldsymbol{x})+\gamma^{S} \mathfrak{R}_{S}(\boldsymbol{x})" border="0"/>

Low-rank 部分：

<img src="http://latex.codecogs.com/svg.latex? \mathfrak{R}_{L R}(\boldsymbol{x})=\min _{\left\{\boldsymbol{D}_{i}\right\}} \sum_{i=1}^{N}\left\{\left\|V_{i} \boldsymbol{x}-\boldsymbol{D}_{i}\right\|_{F}^{2}+\theta^{2} \operatorname{rank}\left(\boldsymbol{D}_{i}\right)\right\}" border="0"/>

sparsity 部分：

<img src="http://latex.codecogs.com/svg.latex? \mathfrak{R}_{S}(\boldsymbol{x}, \boldsymbol{W})=\min _{\left\{\alpha_{i}\right\}} \sum_{i=1}^{N}\left\{\left\|\boldsymbol{W} C_{i} \boldsymbol{x}-\alpha_{i}\right\|_{2}^{2}+\lambda^{2}\left\|\alpha_{i}\right\|_{0}\right\}" border="0"/>

sparsity 部分还会限制 $\mathbf{W}$ 是 $\boldsymbol{W}^{H} \boldsymbol{W}=\boldsymbol{I}_{n l}$

贴一个算法图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200210133702.png" width="50%"/>

### Bibtex:

```latex
@article{wen2018power,
  title={The power of complementary regularizers: Image recovery via transform learning and low-rank modeling},
  author={Wen, Bihan and Li, Yanjun and Bresler, Yoram},
  journal={arXiv preprint arXiv:1808.01316},
  year={2018}
}
```

