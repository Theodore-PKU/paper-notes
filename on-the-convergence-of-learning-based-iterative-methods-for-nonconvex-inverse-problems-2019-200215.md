### Paper:

On the Convergence of Learning-based Iterative Methods for Nonconvex Inverse Problems

### Author:

Risheng Liu, Shichao Cheng, Yi He, Xin Fan, Zhouchen Lin, and Zhongxuan Luo

### Year:

2019

### Notes:

阅读日期：2020.02.15

泛读。这篇文章研究的还是展开式算法的收敛性问题。不过这里的展开算法，都是借用一个训练好的模型来计算或作为原来传统算法中的中间值。作者一共给出了两种改进算法来保证收敛性，第一种是根据目标函数值的变化，第二种是从梯度构建了一个判定条件，并给出了相关的证明和结论。

因为考虑的是逆问题，所以目标函数是：

<img src="http://latex.codecogs.com/svg.latex? \min _{\mathbf{x}} \Psi(\mathbf{x}):=f(\mathbf{x} ; \mathcal{T}, \mathbf{y})+g(\mathbf{x})" border="0"/>

在这篇文章中：$A_g,A_f$ 分别表示关于函数 $g,f$ 的额外训练的由用户自定的模型。

作者给出的第一个算法

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200215133711.png" width="60%"/>

作者给出的第二个算法：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200215133719.png" width="60%"/>

在第二个算法中，相关定义是：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{d}_{\Psi^{k}}^{\mathrm{x}}=\mathbf{d}_{g}^{\mathrm{x}}+\nabla f(\mathbf{x})+\mu^{k}\left(\mathbf{x}-\mathbf{x}^{k}\right) \in \partial \Psi^{k}(\mathbf{x})" border="0"/>

通过数学推导，实际的计算：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{d}_{\Psi^{k}}^{\tilde{\mathbf{u}}^{k}}=\left(\mu^{k}-1 / \gamma^{k}\right)\left(\tilde{\mathbf{u}}^{k}-\mathbf{u}^{k}\right)-\left(\nabla f\left(\mathbf{u}^{k}\right)-\nabla f\left(\tilde{\mathbf{u}}^{k}\right)\right)" border="0"/>

> 这篇文章提出的方法非常直接，如果额外的模型 $A_g, A_f$ 计算出的 u 满足更低的目标函数值，那么就采用这个中间值，否则重复原始的 ADMM 算法，或者是满足一定的条件。不过这里的问题在于很多方法已经抛弃了原始的目标函数了。prior 包含在 $A_g, A_f$ 中，这样这个做法还有什么意义呢？

### Bibtex:

```latex
@article{liu2019convergence,
  title={On the convergence of learning-based iterative methods for nonconvex inverse problems},
  author={Liu, Risheng and Cheng, Shichao and He, Yi and Fan, Xin and Lin, Zhouchen and Luo, Zhongxuan},
  journal={IEEE transactions on pattern analysis and machine intelligence},
  year={2019},
  publisher={IEEE}
}
```

