### Paper:

Unsupervised Deep Basis Pursuit: Learning inverse problems without ground-truth data

### Author:

Jonathan I. Tamir, Stella X. Yu, Michael Lustig

### Year:

2019

### Notes:

阅读日期：2020.02.28

泛读。这篇文章研究的是 inverse problem，实际的应用是 MRI。使用的方法其实总体上是 unroll 形式的网络，但是作者考虑没有 full-sample 的数据，也就是无法实现有监督学习。对此作者提出的方法其实是在损失函数上进行改动，类似于 DIP 的损失函数，即在 kspace 上的 sample 部分的损失函数。另外作者的 unroll 方法也比一般的一阶梯度方法复杂一点。网络CNN 部分用的是 U-net。

设目标函数为：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{ll}\arg \min _{\boldsymbol{x}} & \left.\frac{1}{2} \| \mathcal{N}_{\boldsymbol{w}}(\boldsymbol{x})\right) \|_{2}^{2} \\ \text { subject to } & \|\boldsymbol{y}-\boldsymbol{A x}\|_{2} \leq \epsilon\end{array}" border="0"/>

这里已经用了网络来表示正则项了，网络的作用基本上就是去噪，所以这里的 N 表示的是残差。

整体上的迭代是两步：（这里其实和一般方法有一些区别。因为这里的约束没有归到目标函数中去）

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \boldsymbol{r}_{k} &=\mathcal{R}_{\boldsymbol{w}}\left(\boldsymbol{x}_{k-1}\right) \\ \boldsymbol{x}_{k} &=\arg \min _{\boldsymbol{x}} \frac{1}{2}\left\|\boldsymbol{x}-\boldsymbol{r}_{k}\right\|_{2}^{2} \text { s.t. }\|\boldsymbol{y}-\boldsymbol{A} \boldsymbol{x}\|_{2} \leq \epsilon \end{aligned}" border="0"/>

第二步用 ADMM 算法求解，ADMM 的第一步用 CG 算法求解：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \boldsymbol{x}_{l} &=\left(\rho \boldsymbol{A}^{*} \boldsymbol{A}+\boldsymbol{I}\right)^{-1}\left(\rho \boldsymbol{A}^{*}\left(\boldsymbol{z}_{l-1}-\boldsymbol{u}_{l-1}\right)+\boldsymbol{r}_{k}\right) \\ \boldsymbol{z}_{l} &=\boldsymbol{y}+\operatorname{L}2 \operatorname{Proj}\left(\boldsymbol{A} \boldsymbol{x}_{l}+\boldsymbol{u}_{l-1}-\boldsymbol{y}, \epsilon\right) \\ \boldsymbol{u}_{l} &=\boldsymbol{u}_{l-1}+\boldsymbol{A} \boldsymbol{x}_{l}+\boldsymbol{z}_{l} \end{aligned}" border="0"/>

因此整体的网络结构由一部分是ADMM的迭代步骤。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200228233611.png" width="60%"/>

损失函数：

<img src="http://latex.codecogs.com/svg.latex? \min _{\tilde{w}} \frac{1}{L} \sum_{i=1}^{L} \hat{\mathcal{L}}\left(\boldsymbol{A}^{(i)} \hat{\boldsymbol{x}}_{\tilde{\boldsymbol{w}}}^{(i)} \boldsymbol{y}^{(i)}\right)" border="0"/>

从实验结果上看，还是比一般的有监督要差一些。

### Bibtex:

```latex
@article{tamir2019unsupervised,
  title={Unsupervised Deep Basis Pursuit: Learning inverse problems without ground-truth data},
  author={Tamir, Jonathan I and Yu, Stella X and Lustig, Michael},
  journal={arXiv preprint arXiv:1910.13110},
  year={2019}
}
```
