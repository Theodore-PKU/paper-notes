### Paper:

Nonlocal Low-Rank Tensor Factor Analysis for Image Restoration

### Author:

Xinyuan Zhang, Xin Yuan, Lawrence Carin

### Year:

2018

### Notes:

这篇文章虽然也是解决逆问题，但是思路是从低秩分解的角度，并且深度学习的用途主要是用来计算矩阵的逆运算。在一般的基于低秩分解的图像处理方法中，会将patch拉直为向量后拼成矩阵，再进行低秩分解。作者认为这样的拉直处理会丢失空间信息，因此作者考虑对张量进行低秩分解，忽略拉直的操作。

首先介绍张量的低秩分解。秩为 1 的张量可以表示为：
$$
\mathcal{T}=\lambda \boldsymbol{a} \otimes \boldsymbol{b} \otimes \boldsymbol{c}\\
\mathcal{T}(i, j, k)=\lambda \boldsymbol{a}(i) \boldsymbol{b}(j) \boldsymbol{c}(k)
$$
其中，$\boldsymbol{a} \in \mathbb{R}^{I}, \boldsymbol{b} \in \mathbb{R}^{J}, \text { and } \boldsymbol{c} \in \mathbb{R}^{K}, \mathcal{T} \in \mathbb{R}^{I \times J \times K}$，$\lambda$ 是一个标量，$a, b, c$ 都是单位向量。秩为 R 的张量相应的可以表示为：
$$
\mathcal{T}=\sum_{r=1}^{R} \lambda_{r} \boldsymbol{a}_{r} \otimes \boldsymbol{b}_{r} \otimes \boldsymbol{c}_{r}\\
\mathcal{T}(i, j, k)=\sum_{r=1}^{R} \lambda_{r} \boldsymbol{a}_{r}(i) \boldsymbol{b}_{r}(j) \boldsymbol{c}_{r}(k)
$$
而张量的低秩分解就是将 $\mathcal{T}$ 用少数几个秩为1 的张量的和，因此是一个优化问题：
$$
\min _{\mathbf{A}, \mathbf{B}, \mathbf{C}, \boldsymbol{\lambda}}\left\|\mathcal{T}-\sum_{r=1}^{R} \lambda_{r} \boldsymbol{a}_{r} \otimes \boldsymbol{b}_{r} \otimes \boldsymbol{c}_{r}\right\|_{F}^{2}
$$
作者给出了一个计算低秩分解的算法。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-28%E4%B8%8B%E5%8D%887.45.34.png" style="zoom:50%;" />

假设我们可以对任意张量进行低秩分解。那么如何重建信号呢？作者给出的方法为：

1. 用某种快速算法得到信号的重建初值 $\hat{x}$。

2. 分解 $\hat{x}$ 为 overlapping patches

3. 使用 k 近邻方法将 patch 分组，对每一组 patch，将它们堆叠起来成为一个三维张量。

4. 对这些张量进行低秩分解，即用 $\lambda$ 最大的那几个分解因子来表示该张量。

5. 将低秩张量对应的patch 重新调整为信号 $\mathcal{L}_p$，用 ADMM 算法求解（第一项就是 data fidelity，因此这是一个平衡的解）：
   $$
   \hat{\boldsymbol{x}}=\arg \min _{\boldsymbol{x}}\|\boldsymbol{y}-\boldsymbol{\Phi} \boldsymbol{x}\|_{2}^{2}+\eta \sum_{p}\left\|\tilde{\mathcal{T}}_{p} \boldsymbol{x}-\mathcal{L}_{p}\right\|_{F}^{2}
   $$

重复 2 到 5 这几步多次，就得到重建结果。在 ADMM 的计算中（其他计算这里不说明了），其中有一步需要计算 $\boldsymbol{x}^{t+1}=\mathbf{U}^{-1}\left(\mathbf{\Phi}^{H} \boldsymbol{y}+\beta \boldsymbol{z}^{t+1}-\frac{\boldsymbol{\mu}^{t}}{2}\right)$，$\mathbf{U}^{-1}=\left(\mathbf{\Phi}^{H} \mathbf{\Phi}+\beta \mathbf{I}\right)^{-1}$ 这个计算可能非常困难（在某些情况下，可以用其他方法求解，比如傅立叶变换）。因此作者考虑用神经网络来计算矩阵的逆。

首先有变形 $\mathbf{U}^{-1}=\beta^{-1}\left(\mathbf{I}-\mathbf{\Phi}^{H} \mathbf{V}^{-1} \mathbf{\Phi}\right), \mathbf{V}=\beta \mathbf{I}+\mathbf{\Phi} \mathbf{\Phi}^{H}$，因此通过估计 $\mathbf{V}^{-1}$ 来计算矩阵逆运算。设网络为 $C_{\theta}$，那么在某个数据集上（必须和处理的数据相关才可以）优化：
$$
\arg \min _{\theta} \mathbb{E}_{\epsilon}\left[\left\|\epsilon-\mathcal{C}_{\theta} \mathbf{V} \epsilon\right\|_{2}^{2}+\left\|\epsilon-\mathbf{V} \mathcal{C}_{\theta} \epsilon\right\|\right]
$$
有了 $C_{\theta}$ 来作为逆运算，就可以用下式来替代 (4)
$$
\boldsymbol{x}^{t+1}=\beta^{-1}\left(\mathbf{I}-\boldsymbol{\Phi}^{H} \mathcal{C}_{\theta} \boldsymbol{\Phi}\right)\left(\boldsymbol{\Phi}^{H} \boldsymbol{y}+\beta \boldsymbol{z}^{t+1}-\frac{\boldsymbol{\mu}^{t}}{2}\right)
$$
从作者的实验结果上看，低秩分解这种方法的效果非常好，比其他很多算法效果都要好，（虽然时间消耗应该很大），随便挑了几张图像的压缩感知信号恢复的结果：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-28%E4%B8%8B%E5%8D%887.50.01.png" style="zoom: 50%;" />

### Bibtex:

```latex
@inproceedings{zhang2018nonlocal,
  title={Nonlocal low-rank tensor factor analysis for image restoration},
  author={Zhang, Xinyuan and Yuan, Xin and Carin, Lawrence},
  booktitle={Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition},
  pages={8232--8241},
  year={2018}
}
```