### Paper:

Denoising Auto-encoding Priors in Undecimated Wavelet Domain for MR Image Reconstruction

### Author:

Siyuan Wang, Junjie Lv, Yuanyuan Hu, Dong Liang, Minghui Zhang, Qiegen Liu

### Year:

2019

### Notes:

泛读。这篇文章的大部分内容感觉以前看过了。可能唯一的区别在于自编码模型的输入和输出是小波变换的结果。总的来说，用一个自编码模型训练出一个去噪网络，然后把去噪网络放到正则项中，用 proximal gradient 方法求解。作者分析了去噪网络和先验概率之间的一些关系。去噪自编码的网络结构比较简单，没什么特别的地方。但是输出是噪声，输入应该是事先加上了噪声的。

一般的去噪网络是 $A_{\sigma_{\eta}}$, 实际定义的是 $D_{\sigma_{\eta}}(\Phi(u))=\Phi(u)+\eta-A_{\sigma_{\eta}}(\Phi(u))$

在训练好去噪网络 $D_{\sigma_{\eta}}$ 后，目标函数是（这里的 $\Phi$ 是小波变换，$\eta$ 是噪声）：
$$
\min _{u}\left\|F_{p} u-f\right\|^{2}+\lambda\left\|D_{\sigma_{\eta}}(\Phi(u))-\eta\right\|^{2}
$$
迭代算法可以写成：
$$
\min _{u}\left\|F_{p} u-f\right\|^{2}+\frac{\lambda}{\beta}\left\|u-\left(u^{k}-\beta \nabla G\left(u^{k}\right)\right)\right\|^{2}
$$
其中，$G(u)=\left\|D_{\sigma_{\eta}}(\Phi(u))-\eta\right\|^{2}, \nabla G(u)=\Phi^{T}\left\{\nabla D_{\sigma_{\eta}}^{T}(\Phi(u))\left[D_{\sigma_{\eta}}(\Phi(u))-\eta\right]\right\}$

完整的解析解：
$$
u^{k+1}=\frac{F_{p}^{T} f+\lambda\left[u^{k}-\Phi^{T}\left\{\nabla D_{\sigma_{\eta}}^{T}\left(\Phi\left(u^{k}\right)\right)\left[D_{\sigma_{\eta}}\left(\Phi\left(u^{k}\right)\right)-\eta\right]\right\}\right]}{\left(F_{p}^{T} F_{p}+\lambda\right)}
$$
<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200323213637.png"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200323214008.png"/>

### Bibtex:

```latex
@article{wang2019denoising,
  title={Denoising Auto-encoding Priors in Undecimated Wavelet Domain for MR Image Reconstruction},
  author={Wang, Siyuan and Lv, Junjie and Hu, Yuanyuan and Liang, Dong and Zhang, Minghui and Liu, Qiegen},
  journal={arXiv preprint arXiv:1909.01108},
  year={2019}
}
```

### 其他

