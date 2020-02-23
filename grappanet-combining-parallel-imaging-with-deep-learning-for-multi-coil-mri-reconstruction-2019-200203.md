### Paper:

GrappaNet: Combining Parallel Imaging with Deep Learning for Multi-Coil MRI Reconstruction

### Author:

Anuroop Sriram, Jure Zbontar, Tullie Murrel, C. Lawrence Zitnick, Aaron Defazio, Daniel K. Sodickson

### Year:

2019

### Notes:

阅读时间：2020.02.03

精读。这篇文章研究的是 parallel imaging 和压缩感知结合的 MRI 重建。使用的方法是深度学习，作者使用一个简单的神经网络来估计 GRAPPA 算法中的卷积核，这个算是作者为了结合两者设想的方案。这样的好处是可以进行端到端的训练。整体的计算流程是欠采样的 multi-coil kspace 输入到一个 CNN 中，输出仍是 multi-coil kspace，然后用估计的卷积核进行卷积运算（相当于 GRAPPA 算法，是对 kspace 的插值），然后再输入到一个 CNN 中，最后经过傅立叶逆变换，以及把 multi-coil images 合并为一个 single image。

论文中提到了一些关于 parallel imaging 的细节。首先每次扫描时，每个线圈的 sensitive 矩阵都会变化，所以每次都需要估计。在 GRAPPA 算法中，使用的是 full sample 的 kspace 中心区域来估计所需要的卷积核，这个方法相当于对 kspace 进行插值。我比较疑惑的是，对于欠采样区域，如何用卷积操作来插值。数学的表示为：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{k}^{u}=G * \mathbf{k}" border="0"/>

$k^u$ 是 un-observed kspace，G 是估计的卷积核。

传统方法是用kspace 中心区域 Auto-Calibration Signal or ACS 来估计

<img src="http://latex.codecogs.com/svg.latex? \hat{G}=\operatorname{argmin}_{G}\left\|\mathbf{k}^{u \prime}-G * \mathbf{k}^{\prime}\right\|^{2}" border="0"/>

作者提出的方法中，整体的流程可以写为：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{x}=h\left(f_{2}\left(G * f_{1}(\mathbf{k})\right)\right)" border="0"/>

$f_1,f_2$ 分别表示两个 CNN，网络结构见右图。第一个部分的输入输出都是 kspace，第二个部分输入输出是图像。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-03%E4%B8%8B%E5%8D%8810.28.4.png"/>

RSS 表示：

<img src="http://latex.codecogs.com/svg.latex? R S S\left(\mathbf{x}_{1}, \ldots \mathbf{x}_{N}\right)=\left(\sum_{n=1}^{N}\left|\mathbf{x}_{n}\right|^{2}\right)^{1 / 2}" border="0"/>

注意，所有的网络都不使用 single-coil，都是当成 multi-coil，因为是复数，所以也使用了双通道的技巧。

U-Net 的结构为：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-03%E4%B8%8B%E5%8D%8810.43.45.png"/>

因为 sensitive 矩阵总是会变化，所以作者用神经网络来估计。输入是 ACS region，输出是卷积核。

损失函数为 SIMM loss 和 1 范数 loss：

<img src="http://latex.codecogs.com/svg.latex? J(\hat{\mathbf{x}}, \mathbf{x})=-\operatorname{SSIM}(\hat{\mathbf{x}}, \mathbf{x})+\lambda\|\hat{\mathbf{x}}-\mathbf{x}\|_{1}" border="0"/>

### Bibtex:

```latex
@article{sriram2019grappanet,
  title={GrappaNet: Combining Parallel Imaging with Deep Learning for Multi-Coil MRI Reconstruction},
  author={Sriram, Anuroop and Zbontar, Jure and Murrell, Tullie and Zitnick, C Lawrence and Defazio, Aaron and Sodickson, Daniel K},
  journal={arXiv preprint arXiv:1910.12325},
  year={2019}
}
```

