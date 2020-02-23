### Paper:

Real-Time Image Reconstruction for Low-Dose CT using Deep Convolutional Generative Adversarial Networks (GANs)

### Author:

Kihwan Choi, Sung Won Kim, and Joon Seok Lim

### Year:

2018

### Notes:

阅读时间：2020.01.27

泛读。这篇文章研究的是 low-dose CT 的重建，用的方法非常简单，就是 GAN + 各种loss。生成网络的结构是 two stride-2 convolutions, several residual blocks, 32 and two fractional-stride convolutions，对抗loss 使用least-squares GAN，损失函数为：

<img src="http://latex.codecogs.com/svg.latex? \mathcal{L}(G, D, X, Y)=\mathcal{L}_{\mathrm{GAN}}(G, D, X, Y)+\lambda_{\mathrm{LDCT}} \mathcal{L}_{\mathrm{LDCT}}(G, X, Y)+\lambda_{\mathrm{IR}} \mathcal{L}_{\mathrm{IR}}(G, X, Y)" border="0"/>

其中

<img src="http://latex.codecogs.com/svg.latex? \mathcal{L}_{\mathrm{LDCT}}(G, X)=\mathbb{E}_{x \sim p_{X}}\left[\|G(x)-x\|_{\ell_{2}}\right]" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \mathcal{L}_{\mathrm{IR}}(G, X, Y)=\mathbb{E}_{(x, y) \sim p_{\mathrm{data}}(x, y)}\left[\|G(x)-y\|_{\ell_{1}}\right]" border="0"/>

$y$ 是用 ASIR 重建的图像，$x$ 是带噪声的 FBP 重建图像，$G$ 表示生成网络。

### Bibtex:

```latex
@inproceedings{choi2018real,
  title={Real-time image reconstruction for low-dose CT using deep convolutional generative adversarial networks (GANs)},
  author={Choi, Kihwan and Kim, Sung Won and Lim, Joon Seok},
  booktitle={Medical Imaging 2018: Physics of Medical Imaging},
  volume={10573},
  pages={1057332},
  year={2018},
  organization={International Society for Optics and Photonics}
}
```

