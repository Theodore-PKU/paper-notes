### Paper:

Convolutional Sparse Coding for Compressed Sensing CT Reconstruction

### Author:

Peng Bao, Wenjun Xia, Kang Yang, Weiyan Chen, Mianyi Chen, Yan Xi, Shanzhou Niu, Jiliu Zhou, He Zhang, Huaiqiang Sun, Zhangyang Wang, and Yi Zhang

### Year:

2019

### Notes:

阅读时间：2020.01.27

泛读。这篇文章研究的是 CT 中的压缩感知问题，使用的基本方法是 Convolutional Sparse Coding（CSC）。具体来说是结合了带权重的最小二乘，先用数据集训练出 CSC 目标函数中的 filter（训练方法是其他人提出来的，这篇文章没怎么说，这样可以避免重建时优化 filter 的大量计算），最后使用交替迭代的方法求解。另外考虑了加入一个 feature map 的TV平方项（这是第二个算法，优化方面和第一个没区别）。关于 TV 平方项，无法理解这样做为什么有效。在实验方面，作者虽然讨论各种超参数的影响，不同的训练 filter 的数据集的影响等，但是也没有提供 feature map 的稀疏性的分析，这导致我无法理解目标函数中的 L-1 正则项的作用。

带权重的最小二乘重建指的是：

<img src="http://latex.codecogs.com/svg.latex? \underset{\boldsymbol{u}}{\arg \min} (\boldsymbol{y}-\boldsymbol{A} \boldsymbol{u})^{T} \boldsymbol{\Sigma}^{-1}(\boldsymbol{y}-\boldsymbol{A} \boldsymbol{u})+\beta R(\boldsymbol{u})" border="0"/>

其中 $\mathbf{\Sigma}$ 表示对角矩阵，每个元素是 $\sigma_{p}^{2}=r_{p} \times \exp \left(\bar{y}_{p} / \varepsilon\right)$，不过文章中没有提到 $r_p$ 是怎么设定的。

CSC 的目标函数是：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\underset{\boldsymbol{u},\left\{M_{i}\right\},\left\{\boldsymbol{f}_{i}\right\}}{\arg \min } \frac{1}{2}(\boldsymbol{y}-\boldsymbol{A} \boldsymbol{u})^{T} \boldsymbol{\Sigma}^{-1}(\boldsymbol{y}-\boldsymbol{A} \boldsymbol{u})+} {\beta\left(\frac{1}{2}\left\|\sum_{i=1}^{N} \boldsymbol{f}_{i} * \boldsymbol{M}_{i}-\boldsymbol{u}\right\|_{2}^{2}+\lambda \sum_{i=1}^{N}\left\|\boldsymbol{M}_{i}\right\|_{1}\right)}\end{array}" border="0"/>

由于 filter 提前训练好了，所以优化的变量不包括 ${f}_i$。使用交替迭代的方法。关于 $u$ 的计算是 separable paraboloid surrogate method，比较复杂。

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \boldsymbol{u}_{q}^{t+1}=\boldsymbol{u}_{q}^{t}-& \frac{\sum_{p=1}^{P}\left(\left(1 / \sigma_{p}^{2}\right) a_{p q}\left(\left[\boldsymbol{A} \boldsymbol{u}^{t}\right]_{p}-\boldsymbol{y}_{p}\right)\right)}{\sum_{p=1}^{P}\left(\left(1 / \sigma_{p}^{2}\right) a_{p q} \sum_{k=1}^{Q} a_{p k}\right)+\beta} \\ &-\frac{\beta\left(\left[\sum_{i=1}^{N} \boldsymbol{f}_{i} * \widetilde{\boldsymbol{M}}_{i}\right]_{q}^{t}-\boldsymbol{u}_{q}^{t}\right)}{\sum_{p=1}^{P}\left(\left(1 / \sigma_{p}^{2}\right) a_{p q} \sum_{k=1}^{Q} a_{p k}\right)+\beta} \\ q=1,2, \ldots, Q \end{aligned}" border="0"/>

关于 $M_i$ 则是一个ADMM算法求解，此处就忽略了。

对于增加了 TV 项（论文中称之为 gradient）

<img src="http://latex.codecogs.com/svg.latex? {\underset{\boldsymbol{u},\left\{M_{i}\right\},\left\{\boldsymbol{f}_{i}\right\}}{\arg \min } \frac{1}{2}(\boldsymbol{y}-\boldsymbol{A} \boldsymbol{u})^{T} \boldsymbol{\Sigma}^{-1}(\boldsymbol{y}-\boldsymbol{A} \boldsymbol{u})+} {\beta\left(\frac{1}{2}\left\|\sum_{i=1}^{N} \boldsymbol{f}_{i} * \boldsymbol{M}_{i}-\boldsymbol{u}\right\|_{2}^{2}+\lambda \sum_{i=1}^{N}\left\|\boldsymbol{M}_{i}\right\|_{1}+ \frac{\tau}{2} \sum_{i=1}^{N}\|\sqrt{\left(\boldsymbol{g}_{0} * \boldsymbol{M}_{i}\right)^{2}+\left(\boldsymbol{g}_{1} * \boldsymbol{M}_{i}\right)^{2}}\|_{2}^{2} \right)}" border="0"/>

主要的变化是在求解 $M_i$ 时的 ADMM 算法做了相应改动，$g_0,g_1$ 分别是梯度算子。

### Bibtex:

```latex
@article{bao2019convolutional,
  title={Convolutional Sparse Coding for Compressed Sensing CT Reconstruction},
  author={Bao, Peng and Xia, Wenjun and Yang, Kang and Chen, Weiyan and Chen, Mianyi and Xi, Yan and Niu, Shanzhou and Zhou, Jiliu and Zhang, He and Sun, Huaiqiang and others},
  journal={IEEE transactions on medical imaging},
  volume={38},
  number={11},
  pages={2607--2619},
  year={2019},
  publisher={IEEE}
}
```

