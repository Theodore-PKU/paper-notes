### Paper:

Optimization Algorithm Inspired Deep Neural Network Structure Design

### Author:

Huan Li, Yibo Yang, Dongmin Chen, Zhouchen Lin

### Year:

2018

### Notes:

泛读。这篇文章并不是和压缩感知有关，但是想法来源于压缩感知领域中将传统算法展开为网络训练的方法。作者的想法是神经网络（除了最后一层）可以看作是某种形式的迭代算法的展开，根据激活函数的不同，可以得到不同的目标函数。而简单的网络前向运算就是某种迭代算法极小化某个目标函数。作者认为如果用更好的迭代算法，就有一个对应的神经网络结构，这样就可以设计出更有效率的网络结构。因此，一句话概括作者的思路就是优化算法和网络结构对应，用优化算法来指导网络结构的设计。

举例。一阶梯度法 $\mathbf{z}_{k+1}=\mathbf{z}_{k}-\nabla f\left(\mathbf{z}_{k}\right)$，如果每一层网络都是 $\mathbf{x}_{k+1}=\Phi\left(\mathbf{W}_{k} \mathbf{x}_{k}\right)$ 的形式，假设 $\mathbf{W}_{k}$ 都是一样的，那么对应神经网络的前向计算就相当于优化如下的目标函数：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-04%E4%B8%8B%E5%8D%882.44.37.png" style="zoom:50%;" />

作者探讨了 Heavy ball 算法 $\mathbf{z}_{k+1}=\mathbf{z}_{k}-\nabla f\left(\mathbf{z}_{k}\right)+\beta\left(\mathbf{z}_{k}-\mathbf{z}_{k-1}\right)$, Nesterov’s accelerated gradient algorithm $\begin{aligned}
&\mathbf{y}_{k}=\mathbf{z}_{k}+\frac{\theta_{k}\left(1-\theta_{k-1}\right)}{\theta_{k-1}}\left(\mathbf{z}_{k}-\mathbf{z}_{k-1}\right)\\
&\mathbf{z}_{k+1}=\mathbf{y}_{k}-\nabla f\left(\mathbf{y}_{k}\right)
\end{aligned}$ 等，得出的结论是，这些算法对应的网络结构就是增加了很多的旁支。ResNet and DenseNet 都可以看成是特例。

### Bibtex:

```latex
@article{li2018optimization,
  title={Optimization algorithm inspired deep neural network structure design},
  author={Li, Huan and Yang, Yibo and Chen, Dongmin and Lin, Zhouchen},
  journal={arXiv preprint arXiv:1810.01638},
  year={2018}
}
```