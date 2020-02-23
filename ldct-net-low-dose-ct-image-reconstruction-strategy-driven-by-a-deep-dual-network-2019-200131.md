### Paper:

LdCT-Net: low-dose CT image reconstruction strategy driven by a deep dual network

### Author:

Ji He, Yongbo Wang, Yan Yang, Zhaoying Bian, Dong Zeng, Jian Sun, Zongben Xu, and Jianhua Ma

### Year:

2019

### Notes:

阅读时间：2020.01.31

泛读。这篇文章研究的是 low-dose 条件下的 CT 重建，这里的 low-dose 没有说是 few views 的情形。作者使用的方法是 unroll 一个 ADMM 算法，不过作者考虑的优化问题和原始的 ADMM 算法的优化问题不同，作者考虑了噪声，给出了一个有一些变化的形式。作者讨论了 unroll 之后，如何初始化（用传统算法的参数初始化），但没有明确说哪些参数是可学习的，也没有说非线性函数怎么表示，总之参考意义不大。

作者给出的优化目标函数是：

<img src="http://latex.codecogs.com/svg.latex? \underset{x, y}{\arg \min } \frac{1}{2}\left\|y-y_{0}\right\|_{\Sigma_{1}^{-1}}^{2}+\frac{\varphi}{2}\|A x-y\|_{\Sigma_{2}^{-1}}^{2}+\lambda R_{1}(y)+\gamma R_{2}(x)" border="0"/>

这里的 $R_1, R_2$ 表示正则项，$y$ 表示无噪声的测量值。$\Sigma_1,\Sigma_2$ 表示带权重的最小二乘（事实上，作者后来把权重都设为1了）。作者使用的正则项上 DCT，$g$ 为 1 范数：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned}{\min _{x, y} \frac{1}{2}\left\|y-y_{0}\right\|_{\Sigma_{1}}^{2}+\frac{\varphi}{2}\|A x-y\|_{\Sigma_{2}^{-1}}^{2}+\sum_{l=1}^{L} \lambda_{l} g\left(D_{l} y\right)+\sum_{k=1}^{K} \gamma_{k} g\left(H_{k} z\right)} \\ {\text { s.t. } z=x}\end{aligned}" border="0"/>

增广拉格朗日函数及四个子问题：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-31%E4%B8%8A%E5%8D%8810.58.04.png"/>

对于这些子问题，作者给出了一阶梯度的迭代算法（不知道作者最后怎么改成网络）

网络使用的 NMSE 为损失函数。网络示意图为：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-31%E4%B8%8A%E5%8D%8811.03.07.png"/>

### Bibtex:

```latex
@inproceedings{he2018ldct,
  title={LdCT-net: low-dose CT image reconstruction strategy driven by a deep dual network},
  author={He, Ji and Wang, Yongbo and Yang, Yan and Bian, Zhaoying and Zeng, Dong and Sun, Jian and Xu, Zongben and Ma, Jianhua},
  booktitle={Medical Imaging 2018: Physics of Medical Imaging},
  volume={10573},
  pages={105733G},
  year={2018},
  organization={International Society for Optics and Photonics}
}
```

