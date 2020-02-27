### Paper:

Learning the Sampling Pattern for MRI

### Author:

Ferdia Sherry, Martin Benning, Juan Carlos De los Reyes, Martin J. Graves, Georg Maierhofer, Guy Williams, Carola-Bibiane Schönlieb and Matthias J. Ehrhardt

### Year:

2019

### Notes:

阅读日期：2020.02.27

泛读。这篇文章研究的是 MRI 的重建，讨论的是如何学到一个 sampling pattern。用的方法是 bilevel optimization。这种方法可以看成类似 min max 两级的优化。在这问题上，给定一个 sampling pattern，可以计算出一个重建结果（这个过程是 lower level 的目标函数的结果），对于该重建结果和 sampling，我们可以设计一个 upper level 的目标函数，度量重建的损失和 sampling 的稀疏程度。关于求解算法，我就不关心了。总之这是一个非深度学习的方法。

在 MRI 问题上，重建问题的一般刻画是：

<img src="http://latex.codecogs.com/svg.latex? \min _{u} \frac{1}{2}\|\mathcal{S F} u-y\|_{2}^{2}+\alpha R(u)" border="0"/>

我们称之为 problem (1)。

<img src="http://latex.codecogs.com/svg.latex? \min _{p} \frac{1}{N} \sum_{i=1}^{N} L_{u_{i}^{*}}\left(\hat{u}_{i}(p)\right)+P(p)" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \text { where } \hat{u}_{i}(p) \text { solves Problem (1) with } y=y_{i}, \mathcal{S}=\mathcal{S}(p), \alpha=\alpha(p)" border="0"/>

上式就是所谓的 bilevel 要优化的问题。$p$ 表示的是 sampling pattern。$L_{u_i^*}$ 是一个度量重建结果的损失函数，$P(p)$ 则是关于 sampling pattern 的罚项。在论文中设计为：

<img src="http://latex.codecogs.com/svg.latex? P(p)=\beta \sum_{i=1}^{n} p_{i}+p_{i}\left(1-p_{i}\right)" border="0"/>

之后遇到 bilevel 这种方法的论文就不再看了。

### Bibtex:

```latex
@article{sherry2019learning,
  title={Learning the sampling pattern for MRI},
  author={Sherry, Ferdia and Benning, Martin and Reyes, Juan Carlos De los and Graves, Martin J and Maierhofer, Georg and Williams, Guy and Sch{\"o}nlieb, Carola-Bibiane and Ehrhardt, Matthias J},
  journal={arXiv preprint arXiv:1906.08754},
  year={2019}
}
```

