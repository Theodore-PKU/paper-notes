### Paper:

Deep Unfolding of a Proximal Interior Point Method for Image Restoration

### Author:

C. Bertocchi, E. Chouzenoux, M.-C. Corbineau, J.-C. Pesquet, and M. Prato

### Year:

2019

### Notes:

阅读日期：2020.02.18

泛读。这篇文章研究的是内点法的网络展开。参数化是对步长什么的进行参数化。从这篇文章了解了内点法的具体内容。因为原始的内点法的 proximal 算子不好计算，所以稍微改了一下。其它没有什么特别的。

假设目标函数是：

<img src="http://latex.codecogs.com/svg.latex? h(x, y, \lambda)=f(H x, y)+\lambda \mathcal{R}(x)" border="0"/>

内点法的特点是，x 是有约束条件的。比如

<img src="http://latex.codecogs.com/svg.latex? x \in \operatorname{int} \mathcal{C}=\left\{x \in \mathbb{R}^{n} |(\forall i \in\{1, \ldots, p\}) c_{i}(x)>0\right\}" border="0"/>

内点法引入了一个 barrier 函数：

<img src="http://latex.codecogs.com/svg.latex? \left(\forall x \in \mathbb{R}^{n}\right) \quad \mathcal{B}(x)=\left\{\begin{aligned}-\sum_{i=1}^{p} \ln \left(c_{i}(x)\right) & \text { if } x \in \operatorname{int} \mathcal{C} \\+\infty & \text { otherwise } \end{aligned}\right." border="0"/>

通过把约束引入优化的目标函数中，得到

<img src="http://latex.codecogs.com/svg.latex? \min _{x \in \mathbb{R}^{n}} f(H x, y)+\lambda \mathcal{R}(x)+\mu \mathcal{B}(x)" border="0"/>

这里的 $\mu$ 会逐渐减小。标准的内点法计算是：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200218220724.png"/>

但是 proximal 算子很可能没有闭式解，所以作者提出了另一个类似于 ISTA 那种 gradient 的算法：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200218220742.png"/>

作者在文章中讨论很多种情形的 $\mathcal{B}$ 的 proximal 计算形式。而作者所谓的 unroll 的网络，其实是来学出 $\lambda, \gamma$ 等参数。作者用三个 hidden structure 来表示 $\mathcal{L}_{k}^{(\mu)}, \mathcal{L}_{k}^{(\gamma)} \text { and } \mathcal{L}_{k}^{(\lambda)}$。这些结构分别是：

<img src="http://latex.codecogs.com/svg.latex? \gamma_{k}=\mathcal{L}_{k}^{(\gamma)}=\text { Softplus }\left(a_{k}\right)" border="0"/>

$\mathcal{L}_{k}^{\mu}$ 是一个网络结构（很简单），$\mathcal{L}_{k}^{(\lambda)}$ 取决于正则项

每步迭代的计算是：

<img src="http://latex.codecogs.com/svg.latex? x_{k+1}=\mathcal{A}\left(x_{k}, \mu_{k}, \gamma_{k}, \lambda_{k}\right) =\operatorname{prox}_{\gamma_{k} \mu_{k} \mathcal{B}}\left(x_{k}-\gamma_{k} \nabla_{1} h\left(x_{k}, y, \lambda_{k}\right)\right)" border="0"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200218223723.png"/>

### Bibtex:

```latex
@article{bertocchi2019deep,
  title={Deep unfolding of a proximal interior point method for image restoration},
  author={Bertocchi, Carla and Chouzenoux, Emilie and Corbineau, Marie-Caroline and Pesquet, Jean-Christophe and Prato, Marco},
  journal={Inverse Problems},
  year={2019},
  publisher={IOP Publishing}
}
```

