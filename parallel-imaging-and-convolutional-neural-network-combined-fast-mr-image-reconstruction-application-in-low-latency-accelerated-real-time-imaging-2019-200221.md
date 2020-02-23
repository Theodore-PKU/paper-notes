### Paper:

Parallel imaging and convolutional neural network combined fast MR image reconstruction: Applications in low-latency accelerated real-time imaging

### Author:

Ziwu Zhou and Fei Han, Vahid Ghodrati and Yu Gao, Wotao Yin, Yingli Yang, Peng Hu

### Year:

2019

### Notes:

阅读日期：2020.02.21

泛读。这篇文章研究的是 parallel mri 的重建，方法非常简单，还是 unroll network，不过这一篇文章 unroll 的迭代式是一阶梯度迭代式，这个比较少见。因为是 parallel mri，所以会事先估计出 sensitive map 之后得到一个合适的 zero-fill 作为网络的输入。其他的没有什么特别的地方。

原始目标函数为（把网络当作是一个去噪网络的正则项）：

<img src="http://latex.codecogs.com/svg.latex? \min _{x}\left\|A_{u} x-y\right\|_{2}^{2}+\lambda\left\|x-M_{C N N}\left(x_{z f} | \theta\right)\right\|_{2}^{2}" border="0"/>

对应的一阶梯度是：

<img src="http://latex.codecogs.com/svg.latex? x^{t+1}=x^{t}-2 \alpha^{t}\left[\lambda\left(x^{t}-M_{C N N}\left(x_{z f} | \theta^{*}\right)\right)+A_{u}^{*}\left(A_{u} x^{t}-y\right)\right]" border="0"/>

因此网络计算是一个模块，剩下的计算当作是 data consistency 的模块。损失函数是 magnitude 的 2 范数 + 网络参数的 2 范数正则化：

<img src="http://latex.codecogs.com/svg.latex? L(\theta)= \min _{\theta} \sum_{i=1}^{P}\left\|\operatorname{Mag}\left(x_{\text {recon}}^{i}(\theta)\right)-\operatorname{Mag}\left(x_{\text {ref}}^{i}\right)\right\|_{1} +\gamma\|\theta\|_{2} " border="0"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200221092501.png"/>

### Bibtex:

```latex
@article{zhou2019parallel,
  title={Parallel imaging and convolutional neural network combined fast MR image reconstruction: Applications in low-latency accelerated real-time imaging},
  author={Zhou, Ziwu and Han, Fei and Ghodrati, Vahid and Gao, Yu and Yin, Wotao and Yang, Yingli and Hu, Peng},
  journal={Medical physics},
  volume={46},
  number={8},
  pages={3399--3413},
  year={2019},
  publisher={Wiley Online Library}
}
```

