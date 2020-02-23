### Paper:

Solving ill-posed inverse problems using iterative deep neural networks

### Author:

Jonas Adler, Özan ktem

### Year:

2017

### Notes:

阅读日期：2020.02.09

泛读。这篇文章研究的是逆问题。大致意思应该是把一个迭代算法中的 gradient 部分当作是一个可学习的算子，用网络来学这个 gradient。但感觉这篇文章有一些细节没写清楚，不是一篇很深度学习的文章。

设我们的目标是：

<img src="http://latex.codecogs.com/svg.latex? \underset{f \in X}{\arg \min }[\mathcal{L}(\mathcal{T}(f), g)+\lambda \mathcal{S}(f)]" border="0"/>

如果用梯度法求解，就变成：

<img src="http://latex.codecogs.com/svg.latex? f_{i}:=f_{i-1}-\sigma\left(\nabla[\mathcal{L}(\mathcal{T}(\cdot), g)]\left(f_{i-1}\right)+\lambda[\nabla \mathcal{S}]\left(f_{i-1}\right)\right) =f_{j}-\sigma[\nabla \mathrm{E}]\left(f_{j}\right)" border="0"/>

如果我们用一个网络来替代 $\mathrm{E}]\left(f_{j}\right)$，就得到作者在这篇文章提出的框架。

### Bibtex:

```latex
@article{adler2017solving,
  title={Solving ill-posed inverse problems using iterative deep neural networks},
  author={Adler, Jonas and {\"O}ktem, Ozan},
  journal={Inverse Problems},
  volume={33},
  number={12},
  pages={124007},
  year={2017},
  publisher={IOP Publishing}
}
```

