### Paper:

Improved low-count quantitative PET reconstruction with a variational neural network

### Author:

Hongki Lim, Student, Il Yong Chun, Yuni K. Dewaraja, and Jeffrey A. Fessler

### Year:

2019

### Notes:

阅读日期：2020.02.18

泛读。这篇文章研究的是 PET 的重建，使用的方法也是 unroll 的形式。原始传统算法是 block coordinate descent (BCD) MBIR algorithm，使用的正则项是卷积表示的 1范数极小。作者展开后，把和正则项有关的卷积替换成可学习的参数，并且单独训练这个部分，即 denoise module。似乎没有全局的训练...

BCD 算法：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned}\left\{\boldsymbol{z}_{k}^{(n+1)}\right\} &=\underset{\left\{\boldsymbol{z}_{k}\right\}}{\arg \min }\left\|\boldsymbol{c}_{k} * \boldsymbol{x}^{(n)}-\boldsymbol{z}_{k}\right\|_{2}^{2}+\alpha_{k}\left\|\boldsymbol{z}_{k}\right\|_{1} \\ &=\mathcal{T}\left(\boldsymbol{c}_{k} * \boldsymbol{x}^{(n)}, \alpha_{k}\right) \\ \boldsymbol{x}^{(n+1)} &=\underset{\boldsymbol{x}}{\arg \min } f(\boldsymbol{x})+\beta\left(\sum_{k=1}^{K}\left\|\boldsymbol{c}_{k} * \boldsymbol{x}-\boldsymbol{z}_{k}^{(n+1)}\right\|_{2}^{2}\right) \end{aligned}" border="0"/>

这里的 $f(x)$ 是 PET 的 data fidelity，第二个子问题需要用 EM 算法求解。

展开后，利用数学的变形，得到的计算框架是：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\boldsymbol{u}^{(n+1)}=\sum_{k=1}^{K} \boldsymbol{d}_{k}^{(n+1)} *\left(\mathcal{T}\left(\boldsymbol{c}_{k}^{(n+1)} * \boldsymbol{x}^{(n)}, \alpha_{k}^{(n+1)}\right)\right)} \\ {\boldsymbol{x}^{(n+1)}=\underset{\boldsymbol{x}}{\arg \min } f(\boldsymbol{x})+\beta\left\|\boldsymbol{x}-\boldsymbol{u}^{(n+1)}\right\|_{2}^{2}}\end{array}" border="0"/>

denoise module 直接用下式训练：

<img src="http://latex.codecogs.com/svg.latex? \underset{\left\{\boldsymbol{c}_{k}\right\},\left\{\boldsymbol{d}_{k}\right\},\left\{\alpha_{k}\right\}}{\arg \min } \sum_{l=1}^{L}\left\|\boldsymbol{x}_{\text {true, }, l}-\sum_{k=1}^{K} \boldsymbol{d}_{k} *\left(\mathcal{T}\left(\boldsymbol{c}_{k} * \boldsymbol{x}_{l}^{(n)}, \alpha_{k}\right)\right)\right\|_{2}^{2}" border="0"/>

算法结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200218115439.png" width="60%"/>

### Bibtex:

```latex
@article{lim2019improved,
  title={Improved low-count quantitative PET reconstruction with a variational neural network},
  author={Lim, Hongki and Chun, Il Yong and Dewaraja, Yuni K and Fessler, Jeffrey A},
  journal={arXiv preprint arXiv:1906.02327},
  year={2019}
}
```

