### Paper:

Unrolled Optimization with Deep Priors

### Author:

Steven Diamond, Vincent Sitzmann, Felix Heide, Gordon Wetzstein

### Year:

2018

### Notes:

泛读。这篇文章的思路是所有的逆问题都可以用一个 unrolled 的网络形式来表达。深度学习在其中起到的是  prior 项的迭代作用。也就是将深度学习的模型作为 prior 的主题，但并不是显示地表达出来，而是在迭代过程中表达出来。

任何逆问题，在贝叶斯的观点下，可以表示为：
$$
x=\underset{x}{\operatorname{argmin}} f(y, A x)+r(x, \theta)
$$
第一项由问题的模型表示，第二项就是 prior。一般来说，迭代算法都包括两步，一步是和 prior 有关的更新，一项是和问题模型有关的更新（往往涉及到 proximal 算子之类的）。作者将第一步用一个CNN来表示，那么就有一个整体框架（以 proximal 迭代算法为例）：

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-04%E4%B8%8B%E5%8D%885.27.06.png)

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-04%E4%B8%8B%E5%8D%885.30.28.png" style="zoom:50%;" />

关于实现的一些细节，作者认为残差网络的结构最适合表示 prior，这也许和残差网络的残差部分可以看作是. $-\alpha_{k} \nabla r\left(x, \theta^{k}\right)$，CNN 的深度和网络的迭代次数具有 tradeoff 关系，信号的初始值用 $A^H y$ 表示是合适的。

这篇论文中，作者讨论了去噪、去模糊和 MRI 三种应用场景。 

### Bibtex:

```latex
@article{diamond2017unrolled,
  title={Unrolled optimization with deep priors},
  author={Diamond, Steven and Sitzmann, Vincent and Heide, Felix and Wetzstein, Gordon},
  journal={arXiv preprint arXiv:1705.08041},
  year={2017}
}
```