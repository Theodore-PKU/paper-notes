### Paper:

Highly undersampled magnetic resonance imaging reconstruction using autoencoding priors

### Author:

Qiegen Liu, Minghui Zhang, Qingxin Yang, Huitao Cheng, Shanshan Wang, Dong Liang

### Year:

2019

### Notes:

阅读日期：2020.02.14

泛读。这篇文章研究的是 MRI 重建，作者使用的方法是先事先训练好一个去噪自编码器，用去噪自编码器的输入和输出的差值作为 prior 项，用去噪自编码器的更新和 data fidelity 的更新作为迭代重建的算法。在原始的去噪自编码器的基础上，作者提出了两个改进，第一个是输入由原来的 single image（单通道）变成多张相同的 image（多通道，论文中取 3）；第二个是使用两种不同水平的噪声，分别先加到输入上，去噪后，得到两个去噪结果（每个都是多通道），对这些结果取平均作为最终的去噪输出。这篇文章以前看过了，基本的想法感觉比较符合一般的思路。比较奇特的是 single channel 变成 multi channel 的作用。以及加不同的噪声给我的感觉是和多尺度有关，因为作者说噪声水平低对应更好的细节，而噪声水平高则是平滑。

原始的求解是：

<img src="http://latex.codecogs.com/svg.latex? \underset{u}{\operatorname{Min}}\left\|u-D_{\sigma_{\eta}}(u)\right\|^{2}+v\left\|F_{p} u-f\right\|^{2}" border="0"/>

整体框架：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200214151851.png"/>

去噪自编码器的结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200214152011.png" width="60%"/>

改进后的目标函数：（这里的 $U = [U,U,U]$）

<img src="http://latex.codecogs.com/svg.latex? \operatorname{Min}_{u} \frac{1}{2}\left\|U-D_{\sigma_{n 1}}(U)\right\|^{2}+\frac{1}{2}\left\|U-D_{\sigma_{n 2}}(U)\right\|^{2}+v\left\|F_{p} u-f\right\|^{2}" border="0"/>

改进后的去噪自编码器的训练损失函数：

<img src="http://latex.codecogs.com/svg.latex? L_{E D A E}=\frac{1}{2}\left\{E_{\eta, U}\left[\left\|U-D_{\sigma_{n 1}}(U)\right\|^{2}\right]+E_{\eta, U}\left[\left\|U-D_{\sigma_{n 2}}(U)\right\|^{2}\right]\right\}" border="0"/>

整体的框架就变成：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200214152614.png"/>

训练后的重建算法：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200214152242.png" width="60%"/>

### Bibtex:

```latex
@article{liu2020highly,
  title={Highly undersampled magnetic resonance imaging reconstruction using autoencoding priors},
  author={Liu, Qiegen and Yang, Qingxin and Cheng, Huitao and Wang, Shanshan and Zhang, Minghui and Liang, Dong},
  journal={Magnetic resonance in medicine},
  volume={83},
  number={1},
  pages={322--336},
  year={2020},
  publisher={Wiley Online Library}
}
```

