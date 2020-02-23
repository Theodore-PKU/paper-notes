### Paper:

Fast Compressive Sensing Recovery Using Generative Models with Structured Latent Variables

### Author:

Shaojie Xu, Sihan Zeng, Justin Romberg

### Year:

2019

### Notes:

阅读时间：2020.01.26

精读。这篇文章研究的是 CS 的重建问题。作者使用的方法是基于 bora 的 GAN 的方法，也就是需要搜索 latent variable z，但是这篇文章中，作者使用了 ADMM 算法来帮助求解，并且提出对 z 进行限制，使其可以更高效的计算。首先，在训练 GAN 时，在一般的对抗 loss 之外，作者借用了 InfoGAN 的想法，each latent variable $z$ is split into codewords $c$ and “random-noise-like” variable $\gamma$，$c$ 用于控制语义信息，$\gamma$ 控制 variation，增加了一个 loss，极大化 $c$ 和 $G_{gen}(z)$ 之间的互信息。这是对 z 的结构上的限制。另外在求解 ADMM 时，使用了一个全连接的神经网络 $G_{proj}$ 来计算关于 $z$ 的子问题，作者在论文中提出了两种不同的训练 $G_{proj}$ 的方法。由于作者使用的数据集都是比较小的图像，所以 $G_{proj}$ 的设计看起来问题不大，但是在更实际的场合，可能就不使用了。

假设 $H(z)$ 是对 $z$ 的限制，那么 ADMM 算法的优化问题可以写为：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned}{\min _{\boldsymbol{x}, \boldsymbol{z}}} & {\|\boldsymbol{y}-\boldsymbol{\Phi} \boldsymbol{x}\|_{2}^{2}+\lambda H(\boldsymbol{z})} \\ {\text {s.t.}} & {\boldsymbol{x}=G_{\text {gen}}(\boldsymbol{z})}\end{aligned}" border="0"/>

子问题的求解写为：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \boldsymbol{x}^{(k+1)} &=\left(\boldsymbol{\Phi}^{T} \boldsymbol{\Phi}+\rho \mathbf{I}\right)^{-1}\left(\boldsymbol{\Phi}^{T} \boldsymbol{y}+\rho\left(G_{g e n}\left(\boldsymbol{z}^{(k)}\right)-\boldsymbol{\mu}^{(k)}\right)\right) \\ \boldsymbol{z}^{(k+1)} &=\arg \min _{\boldsymbol{z}} \lambda H(\boldsymbol{z})+\frac{\rho}{2}\left\|\boldsymbol{x}^{(k+1)}-G_{g e n}(\boldsymbol{z})+\boldsymbol{\mu}^{(k)}\right\|_{2}^{2} \\ \boldsymbol{\mu}^{(k+1)} &=\boldsymbol{\mu}^{(k)}+\boldsymbol{x}^{(k+1)}-G_{g e n}\left(\boldsymbol{z}^{(k+1)}\right) \end{aligned}" border="0"/>

$x$ 子问题是有闭式解的。$z$ 的子问题在 $H(z)$ 已知的情况下可以用 CG 的方法求解。但是作者考虑使用一个 $G_{proj}$ 来直接求解，$G_{proj}$ 的输入就是 $x$，$G_{proj}$ 的结构是 784-1024-512-256-100 的全连接网络。$G_{proj}$ 的训练方法参见下图的算法（概括起来就是需要建立相应的数据 pair 来训练 $x$ 和 $z$ 之间的关系）。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-26%E4%B8%8B%E5%8D%8812.51.37.png" width="50%"/>

### Bibtex:

```latex
@inproceedings{xu2019fast,
  title={Fast Compressive Sensing Recovery Using Generative Models with Structured Latent Variables},
  author={Xu, Shaojie and Zeng, Sihan and Romberg, Justin},
  booktitle={ICASSP 2019-2019 IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP)},
  pages={2967--2971},
  year={2019},
  organization={IEEE}
}
```

