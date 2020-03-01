### Paper:

Solving Inverse Computational Imaging Problems using Deep Pixel-level Prior

### Author:

Akshat Dave, Anil Kumar Vadathya, Ramana Subramanyam, Rahul Baburajan, Kaushik Mitra

### Year:

2018

### Notes:

阅读日期：2020.02.29

泛读。这篇文章研究的是逆问题的重建。作者的思想某种程度上和我差不多。作者认为没有必要每个逆问题都设计一个网络来训练，从 MAP 的角度，只要有一个 image prior 的网络估计就可以了，然后用 MAP 推断就可以得到结果。而 image prior 的估计就意味着对图像概率的一个显式表达，对此，这篇文章采用了自回归模型，比如 pixelCNN 这样的模型。作者认为这种建构 prior 的方式有很多优点，比如可以捕捉远程的 pixel 之间的关联等。

具体来说，pixelCNN 是一个用深度学习来表示 pixel 相对于其他 pixel 的条件概率（链式概率形式），这些条件概率乘起来得到 p(X)。MAP 的推断算法根据模型会有差异，主要的差异来源是模型的噪声水平、投影计算是否有闭式解（难易程度），导致的结果就是使用不同的算法来求解，但是都有一个共通的步骤，就是 p(X) 的梯度的更新，这是唯一设计到网络的部分。作者的 MAP 的算法目标函数是极小化 p(X)，约束是 forward model 以及相关的约束。pixelCNN 是用 patch 训练的，但是计算 MAP 的时候考虑的是全局，所以作者在 MAP 的梯度更新的计算中，会先将图像拆成 patch，计算完概率后，再把 patch 图像（图像的像素会更新，这里作者用了一个技巧，随机地选择一部分 pixel 进行更新）合并起来，进行之后的投影计算。

基本的目标函数：

<img src="http://latex.codecogs.com/svg.latex? \hat{\mathbf{X}}  =\underset{\mathbf{X}}{\arg \max }(\log (p(\mathbf{Y} | \mathbf{X}))+\log (p(\mathbf{X})))" border="0"/>

p(X) 的建模：

![](http://latex.codecogs.com/svg.latex? p(\mathbf{X})=p\left(x_{1}, x_{2}, \ldots, x_{n^{2}}\right)=\prod_{i=1}^{n^{2}} p\left(x_{i} | \mathbf{x}_{<i}\right))

关于 MAP 的算法，作者考虑了三种情况（详细的迭代计算步骤可以看论文）。

1. 噪声很小或没有噪声：projected gradient descent
2. 投影计算没有闭式解：augmented Lagrangian
3. 噪声比较大：gradient descent

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200301123148.png" width="70%"/>

算法流程图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200301123308.png" width="70%"/>

### Bibtex:

```latex
@article{dave2018solving,
  title={Solving inverse computational imaging problems using deep pixel-level prior},
  author={Dave, Akshat and Vadathya, Anil Kumar and Subramanyam, Ramana and Baburajan, Rahul and Mitra, Kaushik},
  journal={IEEE Transactions on Computational Imaging},
  volume={5},
  number={1},
  pages={37--51},
  year={2018},
  publisher={IEEE}
}
```

