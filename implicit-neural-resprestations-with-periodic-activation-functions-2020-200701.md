## Implicit Neural Representations with Periodic Activation Functions

Author: Vincent Sitzmann, Julien N. P. Martel, Alexander W. Bergman, David B. Lindell, Gordon Wetzstein

Year: 2020

```latex
@article{sitzmann2020implicit,
  title={Implicit Neural Representations with Periodic Activation Functions},
  author={Sitzmann, Vincent and Martel, Julien NP and Bergman, Alexander W and Lindell, David B and Wetzstein, Gordon},
  journal={arXiv preprint arXiv:2006.09661},
  year={2020}
}
```

Link: https://github.com/Theodore-PKU/paper-notes/blob/master/implicit-neural-resprestations-with-periodic-activation-functions-2020-200701.md

Note: /Users/xieyutong/Documents/Research/PaperReading/Notes/implicit-neural-resprestations-with-periodic-activation-functions-2020-200701.md



在说明这篇文章的 idea 之前，先介绍要研究的问题。作者考虑的是用神经网络表示一个函数，而这个函数对应的是一个信号。换句话说这个网络表示了一个泛函。比如信号是一个图像，那么网络的输入是坐标，输出是像素值。这个泛函满足一定的条件（可以是边界条件，也可以是梯度的条件），最终目的是找到一个可行的泛函满足这些条件。

问题描述：
$$
\text{find } \Phi(\mathbf{x}), \quad \text{subject to } \mathcal{C}_{m}(\mathbf{a}(\mathbf{x}), \Phi(\mathbf{x}), \nabla \Phi(\mathbf{x}), \ldots)=0, \forall \mathbf{x} \in \Omega_{m}, m=1, \ldots, M
$$
损失函数的构造：
$$
\mathcal{L}=\int_{\Omega} \sum_{m=1}^{M} \mathbf{1}_{\Omega_{m}}(\mathbf{x})\left\|\mathcal{C}_{m}(\mathbf{a}(\mathbf{x}), \Phi(\mathbf{x}), \nabla \Phi(\mathbf{x}), \ldots)\right\| d \mathbf{x}
$$
这篇文章主要的 idea 就是使用 MLP 表示这个泛函的时候（因为泛函本身是信号，所以无法使用更复杂的网络结构），激活函数采用 sine 激活函数，因此这个网络被称为 SIREN。

作者认为 ReLU 的二阶导数为0，无法利用高阶的信息，而 sine 函数的高阶导数是 sine 的平移或 sine 本身，比其他高阶可导的激活函数性质更好。

总的来说，用一个激活函数为 sine 的 MLP 网络（实验中好像都是 5 层网络）表示泛函。损失函数和需要满足的条件有关。比如已知函数的一阶梯度或拉普拉斯算子的结果，那么这个网络对应的一阶梯度和拉普拉斯算子（最终形式也是网络）和已知的结果（对应 label）可以设计出一个损失函数，而每个样本点（也就是网络的输入）都是和信号有关的（因此构造出了数据集）。最终当损失函数降低（也就是约束条件被满足了）得到的最终的泛函就是重建的信号。

这是一种无监督的方法，整体的思路和变分模型（PDE）等是一样的，但是利用网络的形式来表示信号，这里有一个隐含的结构表示。有一些类似 DIP，只不过形式不同，DIP 中网络的输出是目标，而这篇文章中这个网络表示了泛函。

> 从某种意义上说，也可以把这个网络当作是一个巨大的网络的一个小部分，而这个巨大的网络包含了所有的位置，输入是所有坐标的集合。每个地方使用了相同的网络参数。

这篇文章的实验比较多，但思路都是一样的。区别在于构造的损失函数不同。

文章的想法已经说说明清楚，剩下的一个问题是如何初始化网络参数。作者的想法是保证激活值的分布不变，这样以来初始化网络时的最终输出和层数无关。初始化的 weight 如果选取不好，效果会很差。文章的第3小节给出了具体的初始化方法。附录中给出了相关证明。