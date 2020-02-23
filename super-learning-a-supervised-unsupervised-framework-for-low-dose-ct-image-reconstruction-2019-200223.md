### Paper:

SUPER Learning: A Supervised-Unsupervised Framework for Low-Dose CT Image Reconstruction

### Author:

Zhipeng Li, Siqi Ye, Yong Long, Saiprasad Ravishankar

### Year:

2019

### Notes:

阅读日期：2020.02.23

泛读。这篇文章研究的是 low-dose CT 重建，作者的创新的地方在于融合了监督学习和无监督学习的方法。作者并没有提出新的监督学习或无监督学习的方法，而是提出了一个联合的框架。这里的无监督指的就是传统迭代算法。当然作者提出的框架其实很简单，就是一个recurrent 的框架，有监督和无监督交替。如何训练呢？其实也简单，逐层训练。逐层训练的含义就是说先训练一个监督学习的模块，比如以第一个为例，输入是 FBP 重建，用 MSE 训练。其输出作为迭代模块的输入，经过迭代模块之后得到输出，迭代模块的输出作为第二个监督模块训练的输入来训练网络参数。这样就可以逐步训练所有的监督模块的参数。这篇文章的监督模块用的是FBPConvNet，即 U-net 结构，带 residual learning。原始的 FBPConvNet 的输入是 FBP 重建结果。无监督模块用的是 PWLS-EP 或 PWLS-ULTRA。PWLS-EP 的正则项是关于相邻像素点之间的差异。PWLS-ULTRA 的方法比较复杂。总结一下：1. 需要事先用 patch 训练 K 个不同的字典，每个 patch 可以对应不同的字典。2. 重建时，正则项是关于 patch 属于那个类，以及对应的 code 的优化函数。3. 如何知道 patch 属于那个类呢？也需要一个在所有 K 类之间的一个极小化选择。

下面稍微说明一下 PWLS-EP 算法和 PWLS-ULTRA 算法。

目标函数都是：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{x}=\underset{\mathbf{x} \geq \mathbf{0}}{\arg \min }\|\mathbf{y}-\mathbf{A} \mathbf{x}\|_{\mathbf{W}}^{2}+\beta \mathrm{R}(\mathbf{x})" border="0"/>

但是正则项不同。

**PWLS-EP 算法**

<img src="http://latex.codecogs.com/svg.latex? \mathrm{R}(\mathbf{x})=\sum_{j=1}^{N_{p}} \sum_{k \in N_{j}} \kappa_{j} \kappa_{k} \varphi\left(x_{j}-x_{k}\right)" border="0"/>

$N_j$ 是相邻像素。$\varphi(t)=\delta^{2}(|t / \delta|-\log (1+|t / \delta|))$

**PWLS-ULTRA 算法**

<img src="http://latex.codecogs.com/svg.latex? \min _{\left\{\Omega_{k}, \mathbf{Z}_{i}, C_{k}\right\}} \sum_{k=1}^{K} \sum_{i \in C_{k}}\left\{\left\|\mathbf{\Omega}_{k} \mathbf{X}_{i}-\mathbf{Z}_{i}\right\|_{2}^{2}+\eta\left\|\mathbf{Z}_{i}\right\|_{0}\right\}+\sum_{k=1}^{K} \lambda_{k} \mathbf{Q}\left(\mathbf{\Omega}_{k}\right), \text { s.t. } C_{k} \in \mathcal{G} }" border="0"/>

用上式求出不同类的字典，类指的是 patch 的归类。

正则项是：

<img src="http://latex.codecogs.com/svg.latex? \mathrm{R}(\mathbf{x}) \triangleq \min _{\left\{\mathbf{z}_{j}, C_{k}\right\}} \sum_{k=1}^{K} \sum_{j \in C_{k}} \tau_{j}\left\{\left\|\mathbf{\Omega}_{k} \mathbf{P}_{j} \mathbf{x}-\mathbf{z}_{j}\right\|_{2}^{2}+\gamma^{2}\left\|\mathbf{z}_{j}\right\|_{0}\right\}" border="0"/>

每个 patch 的类的判断方法是：

<img src="http://latex.codecogs.com/svg.latex? \underset{1 \leq k \leq K}{\arg \min }\left\|\boldsymbol{\Omega}_{k} \mathbf{P}_{j} \mathbf{x}-H_{\gamma}\left(\mathbf{\Omega}_{k} \mathbf{P}_{j} \mathbf{x}\right)\right\|_{2}^{2}+\gamma^{2}\left\|H_{\gamma}\left(\mathbf{\Omega}_{k} \mathbf{P}_{j} \mathbf{x}\right)\right\|_{0}" border="0"/>

### Bibtex:

```latex
@inproceedings{li2019super,
  title={SUPER Learning: A Supervised-Unsupervised Framework for Low-Dose CT Image Reconstruction},
  author={Li, Zhipeng and Ye, Siqi and Long, Yong and Ravishankar, Saiprasad},
  booktitle={Proceedings of the IEEE International Conference on Computer Vision Workshops},
  pages={0--0},
  year={2019}
}
```

