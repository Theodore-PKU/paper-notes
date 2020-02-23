### Paper:

Compressed Sensing: From Research to Clinical Practice with Data-Driven Learning

### Author:

Joseph Y. Cheng, Feiyu Chen, Christopher Sandino, Morteza Mardani, John M. Pauly, Shreyas S. Vasanawala

### Year:

2019

### Notes:

阅读时间：2020.01.29

精读。这是一篇review文章，没有很特别的地方，但是总结的还不错，有些想法和我的想法差不多。

首先，作者分析了压缩感知传统算法的问题：

1. compressed sensing is sensitive to tuning parameters.
2. compressed sensing may result in textural artifacts
3. compressed sensing usually has unknown reconstruction times

因此考虑用深度学习来克服以上这些问题。对于如下的问题：

<img src="http://latex.codecogs.com/svg.latex? \hat{m}=\arg \min _{m} \frac{1}{2}\|A m-y\|_{2}^{2}+\lambda R(m)" border="0"/>

在迭代算法中，往往用一个 gradient update

<img src="http://latex.codecogs.com/svg.latex? m^{(k+)}=m^{(k)}-2 t A^{H}\left(A m^{(k)}-y\right)" border="0"/>

和与正则项相关的proximal算子来求解，而深度学习往往是用来学习 proximal 算子。

<img src="http://latex.codecogs.com/svg.latex? m^{(k+1)}=\operatorname{prox}_{\lambda R}\left(m^{(k+)}\right)=\arg \min _{u} R(u)+\frac{1}{2 \lambda}\left\|u-m^{(k+)}\right\|_{2}^{2}" border="0"/>

作者在这篇文章中讨论了复数的表示方法：1. 双通道；2. 复数网络。讨论了如何处理 multi-coil，一种做法是网络的输入是经过处理后的 single-channel。讨论了数据预处理和数据增广，其中数据预处理中图像噪声的估计很重要，使用白化处理，数据增广要注意不能产生新的误差。loss 函数，作者讨论了在训练对抗loss 时如何使训练稳定。给出了两个参考的公开数据集 mridata.org 和 fastMRI。（F. Ong, S. Amin, S. S. Vasanawala, and M. Lustig, “An Open Archive for Sharing MRI Raw Data,” in ISMRM & ESMRMB Joint Annual Meeting, Paris, France, 2018, p. 3425. 和 J. Zbontar, F. Knoll, A. Sriram, M. J. Muckley, M. Bruno, A. Defazio, M. Parente, K. J.Geras, J. Katsnelson, H. Chandarana, Z. Zhang, M. Drozdzal, A. Romero, M. Rabbat, P. Vincent, J. Pinkerton, D. Wang, N. Yakubova, E. Owens, C. L. Zitnick, M. P. Recht, D. K. Sodickson, and Y. W. Lui, “fastMRI : An Open Dataset and Benchmarks for Accelerated MRI,” arXiv: 11811.08839 [cs.CV], pp. 1–29, 2018）

另外作者给出了深度学习模型的 demo https://github.com/MRSRL/dl-cs

作者认为专家的评价指标对于重建而言很重要，在 Discussion 中，作者讨论了如何克服数据少的问题，以及一个标准的评价指标的重要性。

一些有趣的点：

1. gradient update 应该是和 data fidelity 项有关的 
2. 对抗 loss 或者说判别器，和 perceptual loss 有点相近，也和 convGenNet 相近。

### Bibtex:

```latex
@article{cheng2019compressed,
  title={Compressed Sensing: From Research to Clinical Practice with Data-Driven Learning},
  author={Cheng, Joseph Y and Chen, Feiyu and Sandino, Christopher and Mardani, Morteza and Pauly, John M and Vasanawala, Shreyas S},
  journal={arXiv preprint arXiv:1903.07824},
  year={2019}
}
```

