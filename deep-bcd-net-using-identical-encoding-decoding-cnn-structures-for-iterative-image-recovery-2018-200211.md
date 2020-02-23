### Paper:

Deep BCD-Net Using Identical Encoding-Decoding CNN Structures for Iterative Image Recovery

### Author:

Il Yong Chun and Jeffrey A. Fessler

### Year:

2018

### Notes:

阅读日期：2020.02.11

泛读。这篇文章是对 BCD-net 方法的一个改进，其改进在于把图像 mapping 中的 encoding 卷积和 decoding 卷积改成相同的卷积。BCD-net 方法总的来说就是图像 mapping 和 data fidelity 之间的交替迭代。整个网络的参数是卷积和 thresholding 的参数。作者使用基于 patch 的逐层训练的方法，每一层的 mapping 的训练方式是先分离每个卷积（及其对应的 thresholding 参数），针对这两个参数交替训练，对于 thresholding 参数，用的是次梯度方法，对于卷积参数，因为有范数的约束，作者用了 ADMM 算法。每训练完一层，就得到新的输出样本。$x_0$ 需要事先定义好。个人感觉这个训练的方式太老旧了，不懂为什么不用一般神经网络的训练方法。另外作者用的 mapping，虽然说是 CNN，但实际上只有两层卷积，而且是基于 patch 的训练，完全没有利用 CNN 的优势。没有什么参考意义。

假设的目标函数：

<img src="http://latex.codecogs.com/svg.latex? \underset{\mathbf{x} \in \mathbb{C}^{N}}{\operatorname{argmin}} \min _{\mathbf{z} \in \mathbb{C}^{N}} f(\mathbf{x} ; \mathbf{y})+\lambda\|\mathbf{x}-\mathbf{z}\|_{2}^{2}+g(\mathbf{z})" border="0"/>

BCD-net 的算法框架：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200211161846.png" width="60%"/>

mapping 的形式：

<img src="http://latex.codecogs.com/svg.latex? \operatorname{Mapping}^{(i)}\left(\mathbf{x}^{(i)}\right):=\sum_{k=1}^{K} \mathbf{S} \mathbf{d}_{k}^{(i)} \otimes \mathcal{T}_{\alpha_{k}^{(i)}}\left(\left(\mathbf{d}_{k}^{(i)}\right)^{*} \otimes \mathbf{x}^{(i)}\right)" border="0"/>

训练过程：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200211161943.png" width="60%"/>

逐层训练的目标函数：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned}\left\{\mathbf{D}^{(i)}, \boldsymbol{\alpha}^{(i)}\right\}=\underset{\{\mathbf{D}, \boldsymbol{\alpha}\}}{\operatorname{argmin}} &\left\|\mathbf{X}_{\text {train }}^{(i)}-\mathbf{D} \mathcal{T}_{\boldsymbol{\alpha}}\left(\mathbf{D}^{H} \mathbf{X}^{(i-1)}\right)\right\|_{F}^{2} \\ & \text { subj. to }\left\|\mathbf{d}_{k}\right\|_{2} \leq 1, \quad k=1, \ldots, K \end{aligned}" border="0"/>

分离后的每个 $d_k,\alpha_k$ 的训练

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned}\left\{\mathbf{d}_{k}^{(i)}, \alpha_{k}^{(i)}\right\}=\underset{\left\{\mathbf{d}_{b}, \alpha_{k}\right\}}{\operatorname{argmin}} &\left\|\mathbf{E}_{k}^{(i)}-\mathbf{d}_{k} \mathcal{T}_{\alpha_{k}}\left(\mathbf{d}_{k}^{H} \mathbf{X}^{(i-1)}\right)\right\|_{F}^{2} \\ & \text { subj. to }\left\|\mathbf{d}_{k}\right\|_{2} \leq 1 \end{aligned}" border="0"/>

其中：

<img src="http://latex.codecogs.com/svg.latex? =\mathbf{E}_{k}^{(i)}:=\mathbf{X}_{\mathrm{train}}^{(i)}-\sum_{k^{\prime} \neq k} \mathbf{d}_{k^{\prime}} \mathcal{T}_{\alpha_{k^{\prime}}}\left(\mathbf{d}_{k^{\prime}}^{H} \widetilde{\mathbf{X}}^{(i)}\right)" border="0"/>

这里的 $\tilde{X}$ 我怀疑写错了，应该是 $X_{train}$.

filter 的训练是 ADMM 算法，目标函数写成：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \mathbf{d}_{k}=& \underset{\mathbf{d}}{\operatorname{argmin}}\left\|\mathbf{E}-\mathbf{d} \mathcal{T}_{\alpha}\left(\mathbf{v}^{H}\right)\right\|_{F}^{2} \\ & \text { subj. to }\|\mathbf{d}\|_{2} \leq 1, \quad \mathbf{v}=\mathbf{X}^{H} \mathbf{d} \end{aligned}" border="0"/>

### Bibtex:

```latex
@inproceedings{chun2018deep,
  title={Deep BCD-net using identical encoding-decoding CNN structures for iterative image recovery},
  author={Chun, Yong and Fessler, Jeffrey A},
  booktitle={2018 IEEE 13th Image, Video, and Multidimensional Signal Processing Workshop (IVMSP)},
  pages={1--5},
  year={2018},
  organization={IEEE}
}
```

