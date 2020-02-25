### Paper:

Consensus Neural Network for Medical Imaging Denoising with Only Noisy Training Samples

### Author:

Dufan Wu, Kuang Gong, Kyungsang Kim, and Quanzheng Li

### Year:

2019

### Notes:

阅读日期：2020.02.25

泛读。这篇文章研究的是医学图像的去噪问题。借用的思想是 noise2noise。noise2noise 的本意是将 clean image 替换为另一个相同内容但噪声不同的图像。在医学图像中，很难得到两个噪声图像，但是可以利用重建的过程构造两个不同的重建结果，即测量值划分为两个子集。但是这样的到的噪声图像比原来的完整测量值重建的图像噪声更大，所以作者提出了一个新的损失函数。另外，作者还给出了 noise2noise 的理论证明，证明了其结果和 noise2clean 的效果相同。但是对此我仍有一些疑问，也许是因为训练数据有限，所以效果不可能完全等价。

作者提出的新的损失函数：

<img src="http://latex.codecogs.com/svg.latex?\begin{aligned} L_{n}=\frac{1}{N} \sum_{i=1}^{N}\{& \frac{1}{2}\left\|f\left(\mathbf{x}_{i}+\mathbf{n}_{i 1} ; \mathbf{\Theta}_{1}\right)-\left(\mathbf{x}_{i}+\mathbf{n}_{i 2}\right)\right\|_{2}^{2}+\\ & \frac{1}{2}\left\|f\left(\mathbf{x}_{i}+\mathbf{n}_{i 2} ; \mathbf{\Theta}_{2}\right)-\left(\mathbf{x}_{i}+\mathbf{n}_{i 1}\right)\right\|_{2}^{2}-\\ &\left.\frac{1}{4}\left\|f\left(\mathbf{x}_{i}+\mathbf{n}_{i 1} ; \mathbf{\Theta}_{1}\right)-f\left(\mathbf{x}_{i}+\mathbf{n}_{i 2} ; \mathbf{\Theta}_{2}\right)\right\|_{2}^{2}\right\} \end{aligned}" border="0"/>

其中 $x_i$ 是原始重建噪声图像，$x_i + n_{i1}, x_i + n_{i2}$ 分别是构造的两个重建的结果。$f$ 表示去噪网络，作者用的是 U-net 的结构。最终的去噪图像是：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{z}_{i}=\frac{f\left(\mathbf{x}_{i}+\mathbf{n}_{i 1} ; \mathbf{\Theta}_{1}\right)+f\left(\mathbf{x}_{i}+\mathbf{n}_{i 2} ; \mathbf{\Theta}_{2}\right)}{2}" border="0"/>

为了增加稳定性，作者增加了两个正则项，一个是网络参数：

<img src="http://latex.codecogs.com/svg.latex? L_{w}=\left\|\boldsymbol{\Theta}_{1}\right\|_{2}^{2}+\left\|\boldsymbol{\Theta}_{2}\right\|_{2}^{2}" border="0"/>

还有一个是图像上要和原始重建结果相近或者是经过去噪算法的原始重建结果：

<img src="http://latex.codecogs.com/svg.latex? L_{r}=\frac{1}{N} \sum_{i=1}^{N}\left\|\mathbf{z}_{i}-\mathbf{x}_{i}^{e s t}\right\|_{2}^{2}" border="0"/>

总的损失函数是：

<img src="http://latex.codecogs.com/svg.latex? L=L_{n}+\beta_{w} L_{w}+\beta_{r} L_{r}" border="0"/>

### Bibtex:

```latex
@inproceedings{wu2019consensus,
  title={Consensus neural network for medical imaging denoising with only noisy training samples},
  author={Wu, Dufan and Gong, Kuang and Kim, Kyungsang and Li, Xiang and Li, Quanzheng},
  booktitle={International Conference on Medical Image Computing and Computer-Assisted Intervention},
  pages={741--749},
  year={2019},
  organization={Springer}
}
```

