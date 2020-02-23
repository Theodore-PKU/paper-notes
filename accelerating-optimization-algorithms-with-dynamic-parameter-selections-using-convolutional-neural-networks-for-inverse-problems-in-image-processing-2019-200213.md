### Paper:

Accelerating Optimization Algorithms With Dynamic Parameter Selections Using Convolutional Neural Networks For Inverse Problems In Image Processing

### Author:

Byung Hyun Lee, Se Young Chun

### Year:

2019

### Notes:

阅读日期：2020.02.13

泛读。这篇文章研究的是一般逆问题的重建。作者的思路有一点奇特，作者的想法是用 CNN 来生成传统迭代算法中的步长。这篇文章没讲 CNN 的结构，只说明了如何训练 CNN。首先作者考虑的算法是 PGM，proximal gradient method，在 step size 拓展之后变成 scaled proximal gradient method。有两个步长，一个是 scalar，一个是 matrix（参见算法图）。训练的方法是：当前迭代结果作为网络输入，输出步长参数，用这个步长更新后的结果和 groundtruth（作者用的 groundtruth 是用 FISTA 算法迭代很多次得到的结果）进行比较，计算 2 范数损失函数，优化参数，使得 loss 最小；接下来用最优的迭代一次的结果再用传统步长迭代一次，作为下一次网络参数优化时的输入。

给出 SPG 算法：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200213171103.png" width="605"/>

**训练过程**

初始的输入：$x^{(k),n}$，网络的输出此时是 $t_{k,n}$

<img src="http://latex.codecogs.com/svg.latex? \tilde{\mathbf{x}}^{(k), n}=\mathcal{T}_{\lambda t}\left(\mathbf{x}^{(k), n}-t_{k, n} \nabla f\left(\mathbf{x}^{(k), n}\right)\right)" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \min _{\Theta} \frac{1}{2}\left\|\mathbf{x}_{*}^{n}-\mathcal{T}_{\lambda t}\left(\mathbf{x}^{(k), n}-t_{k, n}(\mathbf{\Theta}) \nabla \mathbf{f}\left(\mathbf{x}^{(\mathbf{k}), \mathbf{n}}\right)\right)\right\|^{2}" border="0"/>

对上式极小化之后，得到 $\tilde{\mathbf{x}}^{(k), n}$，计算下式得到下一轮的输入：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{x}^{(k+1), n}=\mathcal{T}_{\lambda / L}\left(\tilde{\mathbf{x}}^{(k), n}-t_{L} \nabla f\left(\tilde{\mathbf{x}}^{(k), n}\right)\right." border="0"/>

$\mathcal{I}_{k}=\cup_{i=1}^{k}\left\{\mathbf{x}^{(i), n}, \nabla f\left(\mathbf{x}^{(i), n}\right)\right\}_{n=1}^{N}$，$\mathcal{O_k} = \cup_{i=1}^{k}\left\{\mathbf{x}_{*}^{n}\right\}_{n=1}^{N}$

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200213170910.png" width="50%"/>

另外，文章中还有一些是关于 CNN 计算的参数是否可取的条件，只有满足时才会采用，否则使用传统的步长。感觉这篇文章没啥可取之处，CNN 的原理从直觉上没有看出来。而且作者用 FISTA 算法的结果作为 groundtruth，然后实验中，表情还很差，但是自己的算法表现反而好很多，太奇怪了，用真值还能理解。

### Bibtex:

```latex
搜不到这篇文章....
```

