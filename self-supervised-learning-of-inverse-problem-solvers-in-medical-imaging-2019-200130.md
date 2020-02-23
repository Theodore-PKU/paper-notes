### Paper:

Self-supervised learning of inverse problem solvers in medical imaging

### Author:

Ortal Senouf, Sanketh Vedula, Tomer Weiss, Alex Bronstein, Oleg Michailovich, and Michael Zibulevsky

### Year:

2019

### Notes:

阅读时间：2020.01.30

泛读。这篇文章研究的是 self-supervised learning 的重建方法，用的 fastMRI 的数据来测试。作者的想法和 DIP 非常类似，只不过是对很多数据来训练，而不是单张图片。另外和 DIP 略有区别的地方在于输入不是 random    vaiable，损失函数也不同，DIP 是对一个样本训练，这篇文章则是对多个样本训练。和有监督学习的区别在于某友 groundtruth 作为 label。总的来说，作者只使用了 measurement，在这篇文章，measurement 指的是欠采样情况下的 zero-fill 重建结果。这篇文章中，作者把 mri 的 cs 问题看成两种类型，一种是只选取低频的部分，所以是超分辨，一种是对高频也有采样，所以是 dealiasing，也可以有不同的采样率。

损失函数：

<img src="http://latex.codecogs.com/svg.latex? \mathcal{L}(\mathbf{y}, \hat{\mathbf{y}})=\alpha\|\mathbf{y}-\hat{\mathbf{y}}\|_{1}+\beta\|\mathbf{\Phi} \mathbf{y}-\mathbf{S} \odot \mathbf{\Phi}(\hat{\mathbf{x}})\|_{1}+\gamma\left\|\mathcal{I}_{\theta}(\mathbf{y})-\mathcal{I}_{\theta}(\hat{\mathbf{y}})\right\|_{1}" border="0"/>

在这里，$y$ 表示 measurement，$\hat{x}$ 表示重建结果，$\mathcal{I}_{\theta}(y)$ 就是 $\hat{x}$，$\hat{y}$ 表示重建结果的欠采样下的 zero-fill 重建，$\Phi$ 表示傅立叶变换，$S$ 表示采样。因此第一项是图像域的，因为没有groudtruth，所以只有 zero-fill 意义下的图像，第二项表示傅立叶域的损失函数，第三项表示用 $\hat{y}$ 的重建也应当和 $y$ 的重建一样。这个损失函数最大的问题在于，当 $\mathcal{I}_{\theta}$ 是恒同变换的时候，损失函数为 0. 但是实际训练起来不是这样的结果。

网络的输入，可以用 $y$，也可以用 $z$，$z$ 具体是什么没懂，可能是随机输入，也可以是 $y, z$ 的双通道输入。如果只有 $z$，则损失函数中的第三项 $\gamma$ 只能为 0. 实际上，有的实验中，作者就只用了 $\beta$。

### Bibtex:

```latex
@incollection{senouf2019self,
  title={Self-supervised learning of inverse problem solvers in medical imaging},
  author={Senouf, Ortal and Vedula, Sanketh and Weiss, Tomer and Bronstein, Alex and Michailovich, Oleg and Zibulevsky, Michael},
  booktitle={Domain Adaptation and Representation Transfer and Medical Image Learning with Less Labels and Imperfect Data},
  pages={111--119},
  year={2019},
  publisher={Springer}
}
```

