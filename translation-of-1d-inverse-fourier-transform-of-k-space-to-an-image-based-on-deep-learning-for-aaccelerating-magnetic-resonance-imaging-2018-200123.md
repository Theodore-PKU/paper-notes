### Paper:

Translation of 1D Inverse Fourier Transform of K-space to an Image Based on Deep Learning for Accelerating Magnetic Resonance Imaging

### Author:

Taejoon Eo, Hyungseob Shin, Taeseong Kim, Yohan Jun, and Dosik Hwang

### Year:

2018

### Notes:

阅读时间：2020.01.23

泛读。这篇文章研究的是 MRI 的重建。使用的方法和 domain transform learning 那一篇（nature）的那一篇文章非常相似。nature 的那一篇文章是直接从 kspace 到 image 的映射，使用全连接层时参数量很多。这篇文章则通过先对 kspace 做一次 1 维的傅立叶变换（需要注意此时是沿着行），然后将每一列作为 MLP 的输入，输出是图像的一列。这个 MLP 的参数就会少很多，也就是说 MLP 模拟的是剩下的 1 维傅立叶变换。另外，对于 MLP 的输出，作者再使用一个 residual learning 结构的 CNN 进行伪影去除。最后，作者还加了一个 data fidelity 的运算。

网络结构（注意 MLP 的部分）：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-23%E4%B8%8B%E5%8D%883.52.51.png"/>

CNN 的结构文章中没有具体说明。data fidelity 的运算和一般的做法没有区别。

<img src="http://latex.codecogs.com/svg.latex? \hat{\mathbf{y}}_{\mathrm{f}}\left(\mathrm{k}_{\mathrm{x}}, \mathrm{k}_{\mathrm{y}}\right)=\left\{\begin{array}{cl}{\frac{\hat{\mathrm{y}}_{\mathrm{MK}+\mathrm{CN}}\left(\mathrm{k}_{\mathrm{x}}, \mathrm{k}_{\mathrm{y}}\right)+\lambda \mathrm{y}_{\mathrm{u}}\left(\mathrm{k}_{\mathrm{x}}, \mathrm{k}_{\mathrm{y}}\right)}{1+\lambda}} & {\text { if } \mathrm{U}\left(\mathrm{k}_{\mathrm{x}}, \mathrm{k}_{\mathrm{y}}\right)=1} \\ {\hat{\mathbf{y}}_{\mathrm{MLP}+\mathrm{CNN}}\left(\mathrm{k}_{\mathrm{x}}, \mathrm{k}_{\mathrm{y}}\right)} & {\text { if } \mathrm{U}\left(\mathrm{k}_{\mathrm{x}}, \mathrm{k}_{\mathrm{y}}\right)=0}\end{array}\right." border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \hat{\mathbf{y}}_{\mathrm{MLP}+\mathbf{C N N}}=\mathcal{F}_{2 D}\left(\hat{x}_{\mathrm{MLP}+\mathbf{C N N}}\right)=\mathcal{F}_{2 D}\left(H_{\mathrm{CNN}}\left(\hat{\mathbf{x}}_{\mathrm{MLP}} ; \mathbf{\Theta}_{\mathrm{CNN}}\right)\right)" border="0"/>

### Bibtex:

```latex
@inproceedings{eo2018translation,
  title={Translation of 1d inverse fourier transform of k-space to an image based on deep learning for accelerating magnetic resonance imaging},
  author={Eo, Taejoon and Shin, Hyungseob and Kim, Taeseong and Jun, Yohan and Hwang, Dosik},
  booktitle={International Conference on Medical Image Computing and Computer-Assisted Intervention},
  pages={241--249},
  year={2018},
  organization={Springer}
}
```

