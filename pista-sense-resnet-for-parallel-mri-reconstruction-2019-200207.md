### Paper:

pISTA-SENSE-ResNet for Parallel MRI Reconstruction

### Author:

Tieyuan Lu, Xinlin Zhang, Yihui Huang, Yonggui Yang, Gang Guo, Lijun Bao, Feng Huang, Di Guo, Xiaobo Qu

### Year:

2019

### Notes:

阅读时间：2020.02.07

泛读。这篇文章研究的是 parallel MRI 的重建，使用的方法是 unroll 一个传统算法。这个传统算法感觉和 single-coil 的差别不大，只是在 data fidelity 项有一些变动。在这篇文章中，sensitive matrix 应该是先估计好的。展开的网络结构分为四个部分，data consistency（没有什么特别的，普通的矩阵运算），两个卷积网络替换了原来传统算法中的稀疏变换，原来的 soft-thresholding 的阈值变成可变（可学习）。

<img src="http://latex.codecogs.com/svg.latex? \min _{\mathbf{x}} \lambda\|\mathbf{\Psi} \mathbf{x}\|_{1}+\frac{1}{2} \sum_{j=1}^{J}\left\|\mathbf{U} \mathbf{F} \mathbf{C}_{j} \mathbf{x}-\mathbf{y}_{j}\right\|_{2}^{2}" border="0"/>

传统的展开的算法（pFISTA-SENSE）过程：

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{\mathbf{t}_{s}=\mathbf{x}_{s}+\gamma \sum_{j=1}^{J} \mathbf{C}_{j}^{H} \mathbf{F}^{H} \mathbf{U}^{T}\left(\mathbf{y}_{j}-\mathbf{U F C}_{j} \mathbf{x}_{s}\right)} \\ {\boldsymbol{a}_{s}=\mathbf{\Psi t}_{s}} \\ {\tilde{\boldsymbol{a}}_{s+1}=T_{\lambda \gamma}\left(\boldsymbol{a}_{s}\right)} \\ {\mathbf{x}_{s+1}=\mathbf{\Phi} \tilde{\boldsymbol{a}}_{s+1}}\end{array}\right." border="0"/>

替换后的网络结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-07%E4%B8%8A%E5%8D%8811.24.28.png"/>

其他的一些细节。

损失函数是对每个 block 的输出和 groundtruth 计算的 mse。在每个 block 的 DC modual 之后可以用 residual 的结构，也可以不用，作者的实验表明，residual 的结构更好。对比的方法有传统的 pFISTA-SENSE，深度学习算法 VN 和 MoDL。实验表明，作者提出的方法在各种加速程度下都是最优的。MRI 的欠采样方式是一维笛卡尔 variable sampling。

### Bibtex:

```latex
@article{lu2019pista,
  title={pISTA-SENSE-ResNet for Parallel MRI Reconstruction},
  author={Lu, Tieyuan and Zhang, Xinlin and Huang, Yihui and Yang, Yonggui and Guo, Gang and Bao, Lijun and Huang, Feng and Guo, Di and Qu, Xiaobo},
  journal={arXiv preprint arXiv:1910.00650},
  year={2019}
}
```

