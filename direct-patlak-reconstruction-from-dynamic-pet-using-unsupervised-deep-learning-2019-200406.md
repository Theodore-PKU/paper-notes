### Paper:

Direct Patlak Reconstruction from Dynamic PET Using Unsupervised Deep Learning

### Author:

Kuang Gong, Ciprian Catana, Jinyi Qi, Quanzheng Li

### Year:

2019

### Notes:

泛读。这篇文章和 CT-guided PET parametric image reconstruction using deep neural network without prior training data 非常类似，只不过 model 变了。研究的都不是 PET 重建，而是相关的参数图像（反正不懂是什么）。方法总结起来就是无监督+DIP+ADMM。主要的区别在于目标函数变了，因此 ADMM 算法的第一个子问题不一样。网络结构还是 U-net。作者在论文中提到 dynamic PET 很难有高质量 label 的原因在于扫描时间无法加长，对于 MRI 也是类似的吧。也许可以考虑和静态图像的重建结合起来的迁移学习方法。

目标函数
$$
\max _{\boldsymbol{\alpha}, \boldsymbol{x}} L(\boldsymbol{y} | \boldsymbol{x}), \text { s.t. } \boldsymbol{x}=\left(\boldsymbol{A} \otimes \boldsymbol{I}_{N}\right) f(\boldsymbol{\alpha} | \boldsymbol{z})
$$
ADMM
$$
\begin{aligned}
\boldsymbol{x}^{n+1} &=\arg \max _{\boldsymbol{x}} L(\boldsymbol{y} | \boldsymbol{x})-\frac{\rho}{2}\left\|\boldsymbol{x}-\left(\boldsymbol{A} \otimes \boldsymbol{I}_{N}\right) f\left(\boldsymbol{\alpha}^{n} | \boldsymbol{z}\right)+\boldsymbol{\mu}^{n}\right\|^{2} \\
\boldsymbol{\alpha}^{n+1} &=\underset{\boldsymbol{\alpha}}{\arg \min }\left\|\left(\boldsymbol{A} \otimes \boldsymbol{I}_{N}\right) f(\boldsymbol{\alpha} | \boldsymbol{z})-\left(\boldsymbol{x}^{n+1}+\boldsymbol{\mu}^{n}\right)\right\|^{2} \\
\boldsymbol{\mu}^{n+1} &=\boldsymbol{\mu}^{n}+\boldsymbol{x}^{n+1}-f\left(\boldsymbol{\alpha}^{n+1} | \boldsymbol{z}\right)
\end{aligned}
$$

### Bibtex:

```latex
@inproceedings{gong2019direct,
  title={Direct patlak reconstruction from dynamic PET using unsupervised deep learning},
  author={Gong, Kuang and Catana, Ciprian and Qi, Jinyi and Li, Quanzheng},
  booktitle={15th International Meeting on Fully Three-Dimensional Image Reconstruction in Radiology and Nuclear Medicine},
  volume={11072},
  pages={110720R},
  year={2019},
  organization={International Society for Optics and Photonics}
}
```

### 其他

