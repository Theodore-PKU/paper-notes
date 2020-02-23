### Paper:

Deep Learning-Guided Image Reconstruction from Incomplete Data

### Author:

Brendan Kelly, Thomas P. Matthews, Mark A. Anastasio

### Year:

2017

### Notes:

这篇文章的思路在其他文章里都见到过了。在传统的算法里，逆问题一般都可以写成某种迭代形式，这种迭代形式包含两个部分，一个部分是梯度更新（在很多情形下就是 data fidelity），一个部分是 proximal 算子（对应正则项，或者说将图像映射到真实图像的流形）。作者提出的思路就是将 proximal 算子替换成神经网络，这样就构成一个recurrent 的结构。

迭代算法：
$$
\hat{\mathbf{f}}^{(k+1)}=\mathcal{P}\left(\hat{\mathbf{f}}^{(k)}-\alpha \nabla \mathcal{C}\left(\hat{\mathbf{f}}^{(k)}\right)\right)
$$
损失函数:
$$
\hat{\mathbf{w}}^{*}=\underset{\hat{\mathbf{w}}}{\arg \min } \frac{1}{2} \sum_{i, k}\left\|\mathbf{f}_{(i)}-\mathcal{Q}\left(\hat{\mathbf{f}}_{\mathcal{R},(i)}^{(k)} ; \hat{\mathbf{w}}\right)\right\|_{2}^{2}
$$

### Bibtex:

```latex
@article{kelly2017deep,
  title={Deep learning-guided image reconstruction from incomplete data},
  author={Kelly, Brendan and Matthews, Thomas P and Anastasio, Mark A},
  journal={arXiv preprint arXiv:1709.00584},
  year={2017}
}
```