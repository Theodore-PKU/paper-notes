### Paper:

Learning Personalized Representation for Inverse Problems in Medical Imaging Using Deep Neural Network

### Author:

Kuang Gong, Kyungsang Kim, Jianan Cui, Ning Guo, Ciprian Catana, Jinyi Qi, Quanzheng Li

### Year:

2018

### Notes:

阅读日期：2020.02.09

泛读。这篇文章研究的 PET 的逆问题，用的方法是结合 DIP，也就是说限制图像是一个网络的输出。总体上仍是一个重建问题，求解的时候用 ADMM 算法。其中和网络有关的那一个子问题就相当于一个 2 范数的 loss 下的优化，作者用 L-BFGS 算法求解。

目标函数可以写成：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{cl}{\max } & {L(\boldsymbol{y} | \boldsymbol{x})} \\ {\text { s.t. }} & {\boldsymbol{x}=f(\boldsymbol{\theta} | \boldsymbol{\alpha})}\end{array}" border="0"/>

其中，

<img src="http://latex.codecogs.com/svg.latex? L(\boldsymbol{y} | \boldsymbol{x})=\log \sum_{i=1}^{M} p\left(y_{i} | \boldsymbol{x}\right)" border="0"/>

ADMM 算法：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \boldsymbol{\theta}^{n+1} &=\underset{\boldsymbol{\theta}}{\arg \min }\left\|f(\boldsymbol{\theta} | \boldsymbol{\alpha})-\left(\boldsymbol{x}^{n}+\boldsymbol{\mu}^{n}\right)\right\|^{2} \\ \boldsymbol{x}^{n+1} &=\underset{\boldsymbol{x}}{\arg \max } L(\boldsymbol{y} | \boldsymbol{x})-\frac{\rho}{2}\left\|\boldsymbol{x}-f\left(\boldsymbol{\theta}^{n+1} | \boldsymbol{\alpha}\right)+\boldsymbol{\mu}^{n}\right\|^{2} \\ \boldsymbol{\mu}^{n+1} &=\boldsymbol{\mu}^{n}+\boldsymbol{x}^{n+1}-f\left(\boldsymbol{\theta}^{n+1} | \boldsymbol{\alpha}\right) \end{aligned}" border="0"/>

关于 $x$ 的子问题，作者的求解比较复杂，最后可以化简为：

<img src="http://latex.codecogs.com/svg.latex? x_{j}^{n+1}=\frac{1}{2}\left[f\left(\boldsymbol{\theta}^{n} | \boldsymbol{\alpha}\right)_{j}-\mu_{j}^{n}-A_{. j} / \rho\right]+\frac{1}{2} \sqrt{\left[f\left(\boldsymbol{\theta}^{n} | \boldsymbol{\alpha}\right)_{j}-\mu_{j}^{n}-A_{. j} / \rho\right]^{2}+4 \hat{x}_{j, \mathrm{EM}}^{n+1} A_{. j} / \rho}" border="0"/>

网络结构用的是 U-Net，不过作者加了一些改动。具体可以看示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200209224137.png"/>

### Bibtex:

```latex
@article{gong2018learning,
  title={Learning personalized representation for inverse problems in medical imaging using deep neural network},
  author={Gong, Kuang and Kim, Kyungsang and Cui, Jianan and Guo, Ning and Catana, Ciprian and Qi, Jinyi and Li, Quanzheng},
  journal={arXiv preprint arXiv:1807.01759},
  year={2018}
}
```

