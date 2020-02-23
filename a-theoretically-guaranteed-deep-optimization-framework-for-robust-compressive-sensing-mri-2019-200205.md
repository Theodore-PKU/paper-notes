### Paper:

A Theoretically Guaranteed Deep Optimization Framework for Robust Compressive Sensing MRI

### Author:

Risheng Liu, Yuxi Zhang, Shichao Cheng, Xin Fan, Zhongxuan Luo

### Year:

2019

### Notes:

阅读时间：2020.02.05

泛读。勉为其难地写一下笔记。这篇文章研究 MRI，作者在这篇文章中提出了一种重建的算法框架，希望给算法增加一个收敛性保证。但是这个收敛性是基于一个原始目标函数的，作者设立的目标函数和传统算法一样，是 $l_p$ 范数下小波系数的极小化。深度学习在其中扮演的角色是作为传统算法迭代过程中的中间值，也就是说会利用 CNN 的结果是迭代算法中的一个步骤。为了实现作者所谓的收敛性，对于 CNN 的结果，作者用一个和 moment 相关的式子来判断是否要选择该结果作为中间值，还是直接跳过。另外，作者还提到 MRI 的 magnitude 的噪声不是高斯分布的，而是 Rician 分布。为此，作者改进了一下算法的目标函数，然后改进之后的目标函数很难求解，其中一个子问题，作者直接用神经网络来求解，并且完全没有讲清楚具体的步骤和设计。

下面把作者描述的结合深度学习的传统算法的计算步骤进行说明。

首先，目标函数用了小波变换：

<img src="http://latex.codecogs.com/svg.latex? \Phi(\boldsymbol{\alpha})=\frac{1}{2}\|\mathbf{P} \mathbf{F} \mathbf{A} \boldsymbol{\alpha}-\mathbf{y}\|_{2}^{2}+\lambda\|\boldsymbol{\alpha}\|_{p}" border="0"/>

这个目标函数的求解，包含多个模块。

第一个模块是 data fidelity，注意这里的优化变量是小波系数：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{u}^{k+1}=\arg \min _{\mathbf{u}} \frac{1}{2}\|\mathbf{P} \mathbf{F} \mathbf{A} \mathbf{u}-\mathbf{y}\|_{2}^{2}+\frac{\rho}{2}\left\|\mathbf{u}-\mathbf{\alpha}^{k}\right\|_{2}^{2}" border="0"/>

这个式子有闭式解：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{u}^{k+1}=\mathcal{F}\left(\boldsymbol{\alpha}^{k} ; \rho\right)=\mathbf{A}^{\mathrm{T}} \mathbf{F}^{\mathrm{T}}\left(\mathbf{P}^{\mathrm{T}} \mathbf{P}+\rho \mathbf{I}\right)^{-1}\left(\mathbf{P}^{\mathrm{T}} \mathbf{y}+\rho \mathbf{F} \mathbf{A} \boldsymbol{\alpha}^{k}\right)" border="0"/>

接下来，调用 CNN，作者的输入输出都是小波系数，这个我感到非常差异：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{v}^{k+1}=\mathcal{N}\left(\mathbf{u}^{k+1} ; \vartheta^{k+1}\right)" border="0"/>

然后计算下面这个中间结果，这里的 $f$ 表示 data fideltiy 的式子：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{\beta}^{k+1} \in \operatorname{prox}_{\eta_{1} \lambda_{1} \lambda \|_{p}}\left(\mathbf{v}^{k+1}-\eta_{1}\left(\nabla f\left(\mathbf{v}^{k+1}\right)+\rho\left(\mathbf{v}^{k+1}-\boldsymbol{\alpha}^{k}\right)\right)\right)" border="0"/>

此时通过是否满足下式，来判断接受 $\beta$，还是仅仅保留 $\alpha$:

<img src="http://latex.codecogs.com/svg.latex? \left\|\mathbf{v}^{k+1}-\boldsymbol{\beta}^{k+1}\right\| \leq \varepsilon^{k}\left\|\boldsymbol{\alpha}^{k}-\boldsymbol{\beta}^{k+1}\right\|" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \mathbf{w}^{k+1}=\mathcal{C}\left(\mathbf{v}^{k+1}, \boldsymbol{\alpha}^{k}\right)=\left\{\begin{array}{ll}{\boldsymbol{\beta}^{k+1}} & { \text { Eq. ( }8)} \\ {\boldsymbol{\alpha}^{k}} & {\text { otherwise }}\end{array}\right." border="0"/>

最后再计算一个小波系数：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{\alpha}^{k+1}=\mathcal{P}\left(\mathbf{w}^{k+1} ; \eta_{2}\right) \in \operatorname{prox}_{\eta_{2} \lambda\|\cdot\|_{p}}\left(\mathbf{w}^{k+1}-\eta_{2} \nabla f\left(\mathbf{w}^{k+1}\right)\right)" border="0"/>

总结：个人很不喜欢这个做法，觉得没有什么特别的道理。

### Bibtex:

```latex
@inproceedings{liu2019theoretically,
  title={A theoretically guaranteed deep optimization framework for robust compressive sensing mri},
  author={Liu, Risheng and Zhang, Yuxi and Cheng, Shichao and Fan, Xin and Luo, Zhongxuan},
  booktitle={Proceedings of the AAAI Conference on Artificial Intelligence},
  volume={33},
  pages={4368--4375},
  year={2019}
}
```

