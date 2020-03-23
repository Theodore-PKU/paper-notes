### Paper:

Efficient Fine-tuning of Neural Networks for Artifact Removal In Deep Learning for Inverse Imaging Problems

### Author:

Alice Lucas, Santiago Lopez-Tapia, Rafael Molina, Aggelos K. Katsaggelos

### Year:

2019

### Notes:

泛读。这篇文章要解决的问题是逆问题重建（用深度学习方法）结果的伪影问题。解决的方式是后处理方法，在重建结果的基础上 fine-tune 训练好的网络，使其满足 data consistency。感觉这篇文章就是在原来的训练好网络的基础上，继续调网络参数，使得 DC 条件满足。因为 DC 满足之后，伪影一般就会消失了….因此方法非常类似于 DIP。这篇文章中作者考虑了两个情形，一个是超分辨，一个是 GAN 方法。都没有特别细看具体的内容。作者在超分辨的 fine-tuning 的实验中还对比了 DIP 这种直接在未训练的网络上 fine-tuning 的方法。

超分辨任务
$$
\hat{\psi}=\underset{\psi}{\arg \min }\left\|\mathrm{Af}_{\psi}(\mathrm{y})-\mathrm{y}\right\|_{2}^{2}
$$
GAN 方法

$M(x) = 1 - S(x)$，$S(x)$ 是 Sobel 算子。目的是只考虑边缘。 
$$
\hat{\psi}=\underset{\psi}{\arg \min }\left\|\mathbf{M}(\hat{\mathbf{x}}) \odot\left(\mathrm{Af}_{\psi}(\mathbf{y})_{b i c}-\mathbf{y}_{b i c}\right)\right\|_{2}^{2}
$$

### Bibtex:

```latex
@inproceedings{lucas2019efficient,
  title={Efficient Fine-Tuning of Neural Networks for Artifact Removal in Deep Learning for Inverse Imaging Problems},
  author={Lucas, Alice and Lopez-Tapia, Santiago and Molina, Rafael and Katsaggelos, Aggelos K},
  booktitle={2019 IEEE International Conference on Image Processing (ICIP)},
  pages={3591--3595},
  year={2019},
  organization={IEEE}
}
```

### 其他

