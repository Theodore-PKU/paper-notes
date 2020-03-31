### Paper:

Generative adversarial networks based regularized image reconstruction for PET

### Author:

Zhaoheng Xie, Reheman Baikejiang, Kuang Gong, Xuezhu Zhang, and Jinyi Qi

### Year:

2019

### Notes:

泛读。这篇文章研究的是 PET 重建，使用的方法是 GAN，可以归类于 unroll 类型的方法。类似的方法作者之前已经提出过了，不过这篇文章有一个改进。这篇文章提出的方法，首先是训练一个 GAN 生成 high-dose 图像。GAN 的输入是 five slice low-dose 图像。在生成网络和判别网络中使用了 self-attention 层。在训练好 GAN 之后，构建一个包含 GAN 输入和输出的两个似然函数，最终的重建结果是用 GAN 的输出控制。之前的做法目标函数中只包含了输出的似然函数。因为网络输入也是优化对象，这样做的好处可能在于可以控制网络输入的变化。使用 ADMM 算法求解。

目标函数：
$$
\hat{x}=\arg \max _{x}\left\{\eta L\left(\boldsymbol{y} | \boldsymbol{P} \boldsymbol{\alpha}+\boldsymbol{s}+\boldsymbol{r}))+L(\boldsymbol{y} | \boldsymbol{P} \boldsymbol{x}+\boldsymbol{s}+\boldsymbol{r})\right)\right\}, \quad \text {s.t.}, \boldsymbol{x}=\boldsymbol{f}(\boldsymbol{\alpha})
$$
ADMM 算法：
$$
\begin{aligned}
\boldsymbol{x}^{n+1} &=\underset{x}{\arg \max } L(\boldsymbol{y} | \boldsymbol{P} \boldsymbol{x}+\boldsymbol{s}+\boldsymbol{r})-\frac{\rho}{2}\left\|\boldsymbol{x}-\boldsymbol{f}\left(\boldsymbol{\alpha}^{n}\right)+\boldsymbol{\mu}^{n}\right\|^{2} \\
\boldsymbol{\alpha}^{n+1} &=\underset{\alpha}{\arg \max } \eta L(\boldsymbol{y} | \boldsymbol{P} \boldsymbol{\alpha}+\boldsymbol{s}+\boldsymbol{r})-\frac{\rho}{2}\left\|\boldsymbol{f}(\boldsymbol{\alpha})-\left(\boldsymbol{x}^{n+1}+\boldsymbol{\mu}^{n}\right)\right\|^{2} \\
\boldsymbol{\mu}^{n+1} &=\boldsymbol{\mu}^{n}+\boldsymbol{x}^{n+1}-\boldsymbol{f}\left(\boldsymbol{\alpha}^{n+1}\right)
\end{aligned}
$$
GAN 网络结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200331162057.png"/>

### Bibtex:

```latex
@inproceedings{xie2019generative,
  title={Generative adversarial networks based regularized image reconstruction for PET},
  author={Xie, Zhaoheng and Baikejiang, Reheman and Gong, Kuang and Zhang, Xuezhu and Qi, Jinyi},
  booktitle={15th International Meeting on Fully Three-Dimensional Image Reconstruction in Radiology and Nuclear Medicine},
  volume={11072},
  pages={110720P},
  year={2019},
  organization={International Society for Optics and Photonics}
}
```

### 其他

