### Paper:

Learning Converged Propagations with Deep Prior Ensemble for Image Enhancement

### Author:

Risheng Liu, Long Ma, Yiyang Wang, and Lei Zhang

### Year:

2019

### Notes:

阅读日期：2020.02.24

泛读。这篇文章说是研究 image enhancement，其实基本上就是 inverse problem 的范围。使用的方法可以看成就是一个 urnoll 形式的网络。每个 stage 可以看成是三个部分：1. data fidelity，2. 残差网络计算 enhance direction，3. 投影到合理的值的范围。为了增加收敛性，作者在每个 stage 的最后增加了一个判断条件。若不满足，会对 stage 中的第三步进行重复计算。还是没什么参考价值。

<img src="http://latex.codecogs.com/svg.latex? min _{\mathbf{x}} \Psi(\mathbf{x} ; \mathbf{y}):=f(\mathbf{x} ; \mathbf{y})+g(\mathbf{x})+\mathcal{X}_{\Omega}(\mathbf{x})" border="0"/>

作者给出的网络每个stage的构建形式如下：

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{\dot{\mathbf{x}}^{k+1}=\mathcal{T}_{\eta^{k}}^{f}\left(\mathbf{x}^{k} ; \mathbf{y}\right)} \\ {\ddot{\mathbf{x}}^{k+1}=\dot{\mathbf{x}}^{k+1}-\mathcal{N}^{k}\left(\dot{\mathbf{x}}^{k+1} ; \boldsymbol{\vartheta}^{k}\right)} \\ {\mathbf{x}^{k+1}=\operatorname{prox}_{\mathcal{X}_{\Omega}}\left(\ddot{\mathbf{x}}^{k+1}-\nabla \psi_{\eta^{k}}^{k}\left(\ddot{\mathbf{x}}^{k+1} ; \mathbf{y}\right)\right)}\end{array}\right." border="0"/>

第一个是 Warm Start by Fidelity，其实就是关于 data fidelity 的 proximal 计算：

<img src="http://latex.codecogs.com/svg.latex? \dot{\mathbf{x}}^{k+1}=\mathcal{T}_{\eta^{k}}^{f}\left(\mathbf{x}^{k} ; \mathbf{y}\right):=\arg \min _{\mathbf{x}} f(\mathbf{x} ; \mathbf{y})+\frac{\eta^{k}}{2}\left\|\mathbf{x}-\mathbf{x}^{k}\right\|^{2}" border="0"/>

第二个就是 residual network。

第三个是考虑剩下 $\mathcal{X}_{\Omega}$ 的 proximal 计算，很像 ISTA 算法，其中：

<img src="http://latex.codecogs.com/svg.latex? \psi_{\eta^{k}}^{k}\left(\mathbf{x} ; \mathbf{y}, \mathbf{x}^{k}\right)=f(\mathbf{x} ; \mathbf{y})+g(\mathbf{x})+\frac{\eta^{k}}{2}\left\|\mathbf{x}-\mathbf{x}^{k}\right\|_{2}^{2}" border="0"/>

不过这篇文章感觉对 $g$ 的描述很不明确。给我的感觉就是作者既要包含一个 data dependent 的 prior，即 residual network 部分，还有 hand-designed prior，比如 $g$。

判断条件可以写成：

<img src="http://latex.codecogs.com/svg.latex? \left\|\mathbf{m}^{k+1}\right\| \leq c^{k}\left\|\mathbf{x}^{k+1}-\mathbf{x}^{k}\right\|" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \mathbf{m}^{k+1}=\ddot{\mathbf{x}}^{k+1}-\mathbf{x}^{k+1}+\nabla \psi_{\eta^{k}}^{k}\left(\mathbf{x}^{k+1} ; \mathbf{y}\right)-\nabla \psi_{\eta^{k}}^{k}\left(\ddot{\mathbf{x}}^{k+1} ; \mathbf{y}\right)" border="0"/>

整体的流程图为：

<img src="https://cdn.mathpix.com/snip/images/ZqS-kIVSuH-gLB_OHeR1RUyB3QD93TfRPIMhXogiXQs.original.fullsize.png" />

### Bibtex:

```latex
@article{liu2018learning,
  title={Learning converged propagations with deep prior ensemble for image enhancement},
  author={Liu, Risheng and Ma, Long and Wang, Yiyang and Zhang, Lei},
  journal={IEEE Transactions on Image Processing},
  volume={28},
  number={3},
  pages={1528--1543},
  year={2018},
  publisher={IEEE}
}
```

