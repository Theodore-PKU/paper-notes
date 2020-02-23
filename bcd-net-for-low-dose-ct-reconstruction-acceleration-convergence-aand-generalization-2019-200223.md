### Paper:

BCD-Net for Low-dose CT Reconstruction: Acceleration, Convergence, and Generalization

### Author:

Il Yong Chun, Xuehang Zheng, Yong Long, and Jeﬀrey A. Fessler

### Year:

2019

### Notes:

阅读日期：2020.02.23

泛读。这篇文章研究的是 low-dose CT 重建，使用的方法其实和 unroll 传统算法差不多，但是作者没有这么描述自己的方法。作者的做法是去噪自编码和 FISTA 算法的结合，每个 stage 是去噪自编码得到去噪图像，然后用 FISTA 算法计算去噪图像和 data fidelity 之间的平衡。也许是因为 data fidelity 的目标函数使用了带权重的2 范数所以不好算？不知道为什么没有解析解。因此我认为和 unroll 算法差不多。去噪自编码是非常简单的两层卷积自编码，激活函数用的是 soft-thresholding 函数，参数是 exp(alpha) 的形式。因为整体的结构是网络 + 迭代算法，所以无法整体训练，作者的训练方式采用逐层训练，也就是说第 l 层的去噪自编码的输入是上一层的 FISTA 迭代算法的输出。另外自编码网络是 patch 训练的，作者还有一点理论的收敛性分析，但是我感觉和没说一样。没啥参考价值。

去噪自编码的形式：

<img src="http://latex.codecogs.com/svg.latex? \mathcal{D}_{\mathbf{\theta}^{(l+1)}}(\cdot)=\frac{1}{R} \sum_{k=1}^{K} \mathbf{d}_{k}^{(l+1)} \otimes \mathcal{T}_{\exp \left(\alpha_{k}^{(l+1)}\right)}\left(\mathbf{e}_{k}^{(l+1)} \otimes(\cdot)\right)" border="0"/>

假设输出是 $z$，FISTA 算法求解的是：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{x}^{(l+1)}=\underset{\mathbf{x} \succeq \mathbf{0}}{\operatorname{argmin}} F\left(\mathbf{x} ; \mathbf{y}, \mathbf{z}^{(l+1)}\right):=\frac{1}{2}\|\mathbf{y}-\mathbf{A} \mathbf{x}\|_{\mathbf{W}}^{2}+\frac{\beta}{2}\left\|\mathbf{x}-\mathbf{z}^{(l+1)}\right\|_{2}^{2}" border="0"/>

FISTA 算法的具体计算是迭代下式：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \overline{\mathbf{x}}^{(j+1)} &=\left[\mathbf{v}^{(j)}+\mathbf{M}^{-1}\left(\mathbf{A}^{T} \mathbf{W}\left(\mathbf{y}-\mathbf{A} \mathbf{v}^{(j)}\right)-\beta\left(\mathbf{v}^{(j)}-\mathbf{z}^{(l+1)}\right)\right)\right]_{+} \\ \mathbf{v}^{(j+1)} &=\overline{\mathbf{x}}^{(j+1)}+\frac{t_{j}-1}{t_{j+1}}\left(\overline{\mathbf{x}}^{(j+1)}-\overline{\mathbf{x}}^{(j)}\right), \quad \text { where } t_{j+1}=(1+\sqrt{1+4 t_{j}^{2}}) / 2 \end{aligned}" border="0"/>

### Bibtex:

```latex
@inproceedings{chun2019bcd,
  title={BCD-Net for Low-dose CT Reconstruction: Acceleration, Convergence, and Generalization},
  author={Chun, Il Yong and Zheng, Xuehang and Long, Yong and Fessler, Jeffrey A},
  booktitle={International Conference on Medical Image Computing and Computer-Assisted Intervention},
  pages={31--40},
  year={2019},
  organization={Springer}
}
```

