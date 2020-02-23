### Paper:

Model-based Deep Medical Imaging: the roadmap of generalizing iterative reconstruction model using deep learning

### Author:

Jing Cheng, Haifeng Wang, Yanjie Zhu, Qiegen Liu, Qiyang Zhang, Ting Su, Jianwei Chen, Yongshuai Ge, Zhanli Hu, Xin Liu, Hairong Zheng, Leslie Ying, Dong Liang

### Year:

2019

### Notes:

阅读时间：2020.01.27

泛读。这篇文章研究的不是具体某一个医学图像问题的重建，而是一般化的 unroll 传统算法（model based）的框架。作者在 Model Learning: Primal Dual Networks for Fast MR imaging 这篇文章中对 CP 算法采用了这个框架。这个框架表述起来就是逐步将原算法中的某些项或某些子问题替换成神经网络。最开始一般都是针对 proximal 算子，或者和正则项相关的比较难求的子问题，在这篇文章中，作者认为所有的部分都可以逐步替换为神经网络，包括 data consistency。作者考虑了三个算法并对它们进行逐步 relaxation，分别是 CP 算法、ADMM 算法和 ISTA 算法，其中后两者都是之前有过相关论文。作者的分析是从机器学习的 VC 角度，作者认为减少对网络参数的限制，在数据集足够大的情况下，效果会优于限制较多的网络，同样的，当数据较少的时候，限制较多的网络则表现更好。这一点在直觉上也能成立。但是唯一不理解的是 data consistency 的参数化的具体意义。作者并没有分析参数化后 data consistency 的结果是否还能保持这个性质。

下面把算法的 relaxation 过程一一说明。CP 算法在 Model Learning: Primal Dual Networks for Fast MR imaging 已经表述过了，因此这里不记。

ADMM 算法。

1⃣️

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{aligned} M^{(n)}: m^{(n)}=& \frac{\left[\mathrm{A}^{T} f+\rho\left(z^{(n-1)}-\beta^{(n-1)}\right)\right]}{\left(\mathrm{A}^{T} A+\rho I\right)} \\ Z^{(n)}: Z^{(n)}=& \mu_{1} z^{(n, k-1)}+\mu_{2}\left(m^{(n)}+\beta^{(n-1)}\right) \\ &-\sum_{l=1}^{L} \tilde{\lambda}_{l} \psi_{l}^{T} H\left(\psi_{l} z^{(n, k-1)}\right) \\ P^{(n)}: \beta^{(n)}=& \beta^{(n-1)}+\tilde{\eta}\left(m^{(n)}-z^{(n)}\right) \end{aligned}\right." border="0"/>

2⃣️

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{M^{(n)}: m^{(n)}=\gamma_{1} m^{(n, k-1)}+\gamma_{2}\left(z^{(n)}-\beta^{(n-1)}\right)-A^{T} \Gamma\left(A m^{(n, k-1)}, f\right)} \\ {Z^{(n)}: z^{(n)}=\mu_{1} z^{(n, k-1)}+\mu_{2}\left(m^{(n)}+\beta^{(n-1)}\right)-\sum_{l=1}^{L} \widetilde{\lambda}_{l} \psi_{l}^{T} H\left(\psi_{l} z^{(n, k-1)}\right)} \\ {p^{(n)}: \beta^{(n)}=\beta^{(n-1)}+\tilde{\eta}\left(m^{(n)}-z^{(n)}\right)}\end{array}\right." border="0"/>

3⃣️

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{D^{(n)}: d^{(n)}=\Gamma\left(A m^{(n-1)}, f\right)} \\ {M^{(n)}: m^{(n)}=\Pi\left(m^{(n-1)}, z^{(n-1)}-\beta^{(n-1)}, \mathrm{A}^{T} d^{(n)}\right)} \\ {Z^{(n)}: z^{(n)}=\Lambda\left(m^{(n)}+\beta^{(n-1)}\right)} \\ {P^{(n)}: \beta^{(n)}=\beta^{(n-1)}+\tilde{\eta}\left(m^{(n)}-z^{(n)}\right)}\end{array}\right." border="0"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-27%E4%B8%8B%E5%8D%882.4.png"/>

ISTA 算法

1⃣️

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{r^{(n+1)}=m^{(n)}-\rho \mathrm{A}^{T}\left(\mathrm{A} m^{(n)}-f\right)} \\ {m^{(n+1)}=\tilde{G}\left(\operatorname{soft}\left(G\left(r^{(n+1)}\right), \theta\right)\right)}\end{array}\right." border="0"/>

2⃣️

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{d^{(n+1)}=\Gamma\left(\mathrm{Am}^{(n)}, f\right)} \\ {r^{(n+1)}=m^{(n)}-\rho \mathrm{A}^{T} d^{(n+1)}} \\ {m^{(n+1)}=\tilde{G}\left(\operatorname{soft}\left(G\left(r^{(n+1)}\right), \theta\right)\right)}\end{array}\right." border="0"/>

3⃣️

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{d^{(n+1)}=\Gamma\left(\mathrm{A} m^{(n)}, f\right)} \\ {r^{(n+1)}=\Lambda\left(m^{(n)}, A^{T} d^{(n+1)}\right)} \\ {m^{(n+1)}=\tilde{G}\left(\operatorname{soft}\left(G\left(r^{(n+1)}\right), \theta\right)\right)}\end{array}\right." border="0"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-27%E4%B8%8B%E5%8D%882.46..png"/>

损失函数方面，ISTA 算法还有一个恒同变换的 loss，其他两个算法都是只有  MSE loss。

### Bibtex:

```latex
@article{cheng2019model,
  title={Model-based Deep MR Imaging: the roadmap of generalizing compressed sensing model using deep learning},
  author={Cheng, Jing and Wang, Haifeng and Zhu, Yanjie and Liu, Qiegen and Ying, Leslie and Liang, Dong},
  journal={arXiv preprint arXiv:1906.08143},
  year={2019}
}
```

