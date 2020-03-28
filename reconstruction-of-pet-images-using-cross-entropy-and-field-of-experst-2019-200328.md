### Paper:

Reconstruction of PET Images Using Cross-Entropy and Field of Experts

### Author:

Jose Mejia, Alberto Ochoa and Boris Mederos

### Year:

2019

### Notes:

泛读。这篇文章使用的是传统方法求解 PET 的重建问题，没有使用深度学习的方法。这篇笔记主要是对方法做一个简单的数学公式上的论述，以后的研究中，如果遇到一些结合传统算法的工作时，可以参考这里的一些做法，相关的参考文献也许有用。总体框架是一个 MAP 的贝叶斯框架。data fidelity 是用 cross-entropy，prior 是 field of experts。后者的参数需要事先训练（但是不知道怎么训练），而且训练时还用了 CT 和 MRI 的数据，貌似是基于 patch 的。data fidelity 和 prior 构成了优化的目标函数，然后作者给出了一个算法求解。

data fideliyt 的表达式（作者用所谓的 KL 散度来表示分布的相近程度）：
$$
J_{0}(x)=D(y, A x)=\sum_{i}\left(y_{i} \ln y_{i}-y_{i} \ln (A x)_{i}-y_{i}+(A x)_{i}\right)
$$
可以对比一下对数似然函数：
$$
\ln (L(x))=-\sum_{i=1}^{I}\left[\sum_{j=1}^{J} x_{j} a_{i, j}+y_{i} \ln \left(\sum_{j=1}^{J} x_{j} a_{i, j}\right)+\ln \left(y_{i} !\right)\right]
$$
prior 的表达式：
$$
P(x, \Theta)=\frac{1}{Z(\Theta)} \prod_{k=1}^{K} \prod_{i=1}^{N} \phi\left(J_{i}^{T} x_{(k)}, \alpha_{i}\right)\\
\phi\left(J_{i}^{T} x, \alpha_{i}\right)=\left(1+\frac{1}{2}\left(J_{i}^{T} x\right)^{2}\right)^{-\alpha_{i}}
$$
最后的迭代算法：
$$
x_{j}^{(n+1)}=x_{j}^{(n)}-\frac{x_{j}^{(n)}}{\sum_{i} a_{i, j}} \frac{\partial J_{F o E}\left(x^{(n)}\right)}{\partial x_{j}}
$$

### Bibtex:

```latex
@article{mejia2019reconstruction,
  title={Reconstruction of PET images using cross-entropy and field of experts},
  author={Mejia, Jose and Ochoa, Alberto and Mederos, Boris},
  journal={Entropy},
  volume={21},
  number={1},
  pages={83},
  year={2019},
  publisher={Multidisciplinary Digital Publishing Institute}
}
```

### 其他

