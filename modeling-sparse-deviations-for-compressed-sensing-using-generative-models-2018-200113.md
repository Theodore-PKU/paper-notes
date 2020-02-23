### Paper:

Modeling Sparse Deviations for Compressed Sensing using Generative Models

### Author:

Manik Dhar, Aditya Grover, Stefano Ermon

### Year:

2018

### Notes:

阅读日期：2020.01.13

精读。这篇文章考虑的是一般的压缩感知问题。其想法的思考着眼点在于 bora 的那种压缩感知重建算法具有一些缺陷。作者认为生成模型的输出域不一定和真实图像域匹配，而必然导致误差。另外观察到，当测量数量上升时，bora 的那种方法并不会变得更好，效果会被传统的压缩感知算法所超越（它们的误差会收敛到0）。因此作者试图结合两种方法，结合的方式是在生成函数的输出之外加上一个稀疏项来修正信号的重建。作者给出了具体的形式和求解过程，并给出了相关的理论分析。

作者的原始目标函数：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned}\min _{z, \nu}\|\nu\|_{0} \\ \text { s.t. } A(G(z)+\nu)=y \end{aligned}"  border="0"/>

考虑用 1 范数替换，并且改写为无约束的写法（拉格朗日乘子），得到：

<img src="http://latex.codecogs.com/svg.latex? \min _{z, \nu}\|\nu\|_{1}+\lambda\|A(G(z)+\nu)-y\|_{2}^{2}"  border="0"/>

> 其实求解算法可以有很多，不过这种结合确实可以兼顾两种方法。而且这两种方法都是这个目标函数的特例。

还可以考虑对 deviation 加一个变换，变成L：

<img src="http://latex.codecogs.com/svg.latex? \min _{z, \nu}\|B\nu\|_{1}+\lambda\|A(G(z)+\nu)-y\|_{2}^{2}"  border="0"/>

作者的求解方式是一阶梯度算法，作者认为从实际效果上看挺好的，虽然生成模型 G 是非凸的。也可以考虑其他算法。

下面给出作者的理论分析。

传统算法的理论保证是 RIP 或 REC 条件（定义1和定义2）。REC条件保证了测量矩阵 A 的零空间和稀疏信号空间之间的距离。在 bora 的方法中，也可以类似定义，所以有定义3，S-REC，但是这里的参数 $\delta$ 是一个很重要的变量，它必须很小，才能保证类似 REC 的效果。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-13%E4%B8%8B%E5%8D%8811.19.24.png" width="45%"/> <img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-13%E4%B8%8B%E5%8D%8811.22.06.png" width="50%" />

作者给出了自己的方法的相关结论。需要指出的是，引理1给出的是存在性证明，引理2给出的是当生成模型满足李普西次条件的时候，A可以满足S-REC的条件。定理1则是两者的综合。定理2是神经网络作为生成模型的结果。这里最关键的是引理2，但实际上这个条件很弱（否则为什么还需要训练模型），最主要的问题出在 $\delta$ 和 k，这两个很不好满足。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-13%E4%B8%8B%E5%8D%8811.29.53.png"/>

实验方面，作者使用的生成模型是 VAE，对比了纯 VAE 方法，LASSO 方法。数据集用的 MNIST 和 Omniglot 这两个手写字符的数据集。模型表现：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-13%E4%B8%8B%E5%8D%8811.15.28.png"/>

另外作者还考虑了迁移学习，即用一个数据集来训练生成网络，应用于另一个数据集上。但个人感觉这种做法在很多情况是不可行的。这种迁移其实限制性很强。

### Bibtex:

```latex
@article{dhar2018modeling,
  title={Modeling sparse deviations for compressed sensing using generative models},
  author={Dhar, Manik and Grover, Aditya and Ermon, Stefano},
  journal={arXiv preprint arXiv:1807.01442},
  year={2018}
}
```

