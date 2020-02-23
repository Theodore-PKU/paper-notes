### Paper:

Converged Deep Framework Assembling Principled Modules for CS-MRI

### Author:

Risheng Liu, Yuxi Zhang, Shichao Cheng, Zhongxuan Luo, and Xin Fan

### Year:

2019

### Notes:

这篇文章的认为传统CS-MRI算法太慢，深度学习的方法又不具有可靠性。作者认为两者可以结合起来，借助优化方面的技巧来限制深度学习模块重建的结果在能量模型下是收敛的。整体的结构是cascade，每个cascade可以分为四个模块，分别实现fidelity，data-driven-reconstruction，判断重建结果好坏，稀疏性prior。可以认为，这篇文章比较好地结合了各种有助于重建的信息，即测量值约束、深度学习从数据中获取的信息，先验的稀疏性。

总的来说，似乎可以看成是将深度学习作为一个加快目标函数极小的模块。原来的方法收敛较慢，因为是完全基于梯度。而深度学习提供了一种快速收敛的路径。

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-15%E4%B8%8B%E5%8D%886.13.32.png)

原始的小波正则项优化目标：
$$
\boldsymbol{\alpha} \in \arg \min _{\boldsymbol{\alpha}} \frac{1}{2}\|\mathbf{P} \mathbf{F} \mathbf{A} \boldsymbol{\alpha}-\mathbf{y}\|_{2}^{2}+\lambda\|\boldsymbol{\alpha}\|_{p}
$$
$\mathbf{A}$ 是小波逆变换，$\boldsymbol{\alpha}$ 是小波系数，p属于(0,1) 

**fidelity module** $\mathcal{F}(\boldsymbol{\alpha}, \rho)$：
$$
\mathbf{u}^{k+1}=\arg \min _{\mathbf{u}} \frac{1}{2}\|\mathbf{P} \mathbf{F} \mathbf{A} \mathbf{u}-\mathbf{y}\|_{2}^{2}+\frac{\rho}{2}\left\|\mathbf{u}-\mathbf{\alpha}^{k}\right\|_{2}^{2}
$$
这个fidelity 因为是考虑小波系数而不是图像域，所以和一般的求解会有一些不同。闭式解为：
$$
\mathbf{u}^{k+1}=\mathcal{F}\left(\boldsymbol{\alpha}^{k} ; \rho\right)=\mathbf{A}^{\mathrm{T}} \mathbf{F}^{\mathrm{T}}\left(\mathbf{P}^{\mathrm{T}} \mathbf{P}+\rho \mathbf{I}\right)^{-1}\left(\mathbf{P}^{\mathrm{T}} \mathbf{y}+\rho \mathbf{F} \mathbf{A} \boldsymbol{\alpha}^{k}\right)
$$
**Residual learning for module** $\mathcal{N}\left(\mathbf{u}^{k+1} ; \vartheta^{k+1}\right)$

虽然输入是小波系数，但是先转成图像，最后输出也是图像，所以也会再转成小波系数。

网络结构为：dilated convolution + BN + relu，重复五次。最后是一个残差结构。
$$
\mathbf{v}^{k+1}=\mathcal{N}\left(\mathbf{u}^{k+1} ; \vartheta^{k+1}\right)=\mathbf{A}^{\mathrm{T}}\left(\operatorname{Res} \operatorname{Net}\left(\mathbf{A} \mathbf{u}^{k+1} ; \vartheta^{k+1}\right)\right)
$$
**Optimal condition for module**

判断方法很简单，通过计算一个 momentum proximal gradient，判断 $\mathbf{v}^{k+1}$ 的距离。
$$
\boldsymbol{\beta}^{k+1} \in \operatorname{prox}_{\eta_{1} \lambda_{1} \lambda\|\cdot\|_{p}}\left(\mathbf{v}^{k+1}-\eta_{1}\left(\nabla f\left(\mathbf{v}^{k+1}\right)+\rho\left(\mathbf{v}^{k+1}-\boldsymbol{\alpha}^{k}\right)\right)\right)\\
\operatorname{prox}_{\eta \lambda\|\cdot\|_{p}}(\mathbf{v})=\arg \min _{\mathbf{x}} \lambda\|\mathbf{x}\|_{p}+\frac{1}{2}\|\mathbf{x}-\mathbf{v}\|^{2} \text{ 不过几何的含义不懂}\\
\left\|\mathbf{v}^{k+1}-\boldsymbol{\beta}^{k+1}\right\| \leq \varepsilon^{k}\left\|\boldsymbol{\alpha}^{k}-\boldsymbol{\beta}^{k+1}\right\|\text{ 判断准则}\\
\mathbf{w}^{k+1}=\mathcal{C}\left(\mathbf{v}^{k+1}, \boldsymbol{\alpha}^{k} ; \eta_{1}\right)=\left\{\begin{array}{ll}{\boldsymbol{\beta}^{k+1}} & {\text { if }(准则满足) \text { is satisfied }} \\ {\boldsymbol{\alpha}^{k}} & {\text { otherwise }}\end{array}\right.
$$
**Proximal gradient for module**

这个是考虑稀疏性的，在传统的求解目标函数的算法都有这一步。
$$
\boldsymbol{\alpha}^{k+1}=\mathcal{P}\left(\mathbf{w}^{k+1} ; \eta_{2}\right) \in \operatorname{prox}_{\eta_{2} \lambda\|\cdot\|_{p}}\left(\mathbf{w}^{k++1}-\eta_{2} \nabla f\left(\mathbf{w}^{k+1}\right)\right)
$$
借助**Optimal condition for module**，从直观上看是可以感觉到收敛性存在。作者最后也证明了这一点。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-15%E4%B8%8B%E5%8D%886.37.06.png" style="zoom:50%;" />

几个问题：

1. 文中没有说明 $\mathcal{C}$ 这一步的 $\epsilon^k$ 怎么设。
2. 对于复数的讨论很模糊。

另外作者还将这个方法拓展到 parallel imaging 和 rician noise。这个就不多叙述了。不过作者给出的结果，让人奇怪的是非深度学习方法表现得很不错，尤其是 BM3D-MRI等，我觉得很好...可以看看相关论文

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-15%E4%B8%8B%E5%8D%886.38.09.png)

Z. Zhan, J. Cai, D. Guo, Y. Liu, Z. Chen, and X. Qu, “Fast multiclass dictionaries learning with geometrical directions in MRI reconstruction,” IEEE TBME, vol. 63, no. 9, pp. 1850–1861, 2016.

X. Qu, Y. Hou, F. Lam, D. Guo, J. Zhong, and Z. Chen, “Magnetic resonance image reconstruction from undersampled measurements using a patch-based nonlocal operator,” Med. Imag. Anal., vol. 18, no. 6, pp. 843–856, 2014.

E. M. Eksioglu, “Decoupled algorithm for MRI reconstruction using nonlocal block matching model: BM3D-MRI,” J. Math. Imag. Vis., vol. 56, no. 3, pp. 430–440, 2016.

### Bibtex:

```latex
@article{liu2019converged,
  title={Converged Deep Framework Assembling Principled Modules for CS-MRI},
  author={Liu, Risheng and Zhang, Yuxi and Cheng, Shichao and Luo, Zhongxuan and Fan, Xin},
  journal={arXiv preprint arXiv:1910.13046},
  year={2019}
}
```