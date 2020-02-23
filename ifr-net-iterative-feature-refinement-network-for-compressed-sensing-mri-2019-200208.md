### Paper:

IFR-Net: Iterative Feature Refinement Network for Compressed Sensing MRI

### Author:

Yiling Liu, Qiegen Liu, Minghui Zhang, Qingxin Yang, Shanshan Wang and Dong Liang

### Year:

2019

### Notes:

阅读日期：2020.02.08

泛读。这篇文章研究的是 MRI，作者的思路仍然是 unroll 一个算法。这个算法称为 IFR-CS（iterative feature refinement model）。其主要的特点是增加了一个 refine 的操作。作者在展开这个算法的时候，很多地方都借鉴了 ISTA-net 的做法，包括卷积层替代原来的稀疏变换，分片线性函数替换了激活函数等。refine 这部分应该是保持不变，只是其中的一个参数改成可变的参数。所有的 stage 都不共享参数，损失函数是 NMSE。这篇文章最让人困惑的地方在于 refine 部分的图像处理意义是什么，感觉不是很明确。感觉没有特别的参考价值。

**IFR-CS 算法介绍**

考虑了附加变量之后的目标函数是：

<img src="http://latex.codecogs.com/svg.latex? \min _{x, u} \frac{1}{2}\left\|F_{p} x-y\right\|_{2}^{2}+\sum_{l=1}^{L} \lambda_{l}\left\|D_{l} u\right\|_{1}+\frac{\rho}{2}\|x-u\|_{2}^{2}" border="0"/>

对应的迭代算法是：

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{x^{(n+1)}=\arg \min _{x} \frac{1}{2}\left\|F_{p} x-y\right\|_{2}^{2}+\frac{\rho}{2}\left\|x-x_{t}^{(n)}\right\|_{2}^{2}} \\ {u^{(n+1)}=\arg \min _{u} \frac{\rho}{2}\left\|x^{(n)}-u\right\|_{2}^{2}+\sum_{l=1}^{L} \lambda_{l}\left\|D_{l} u\right\|_{1}} \\ {x_{t}^{(n+1)}=u^{(n+1)}+T^{(n+1)} \otimes\left(x^{(n+1)}-u^{(n+1)}\right)}\end{array}\right." border="0"/>

注意，这里的第三步就是 refine 步，稍后会介绍 $T^{(n+1)}$ 的计算方式

流程图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200208145606.png" width="70%"/>

**IFR-net 对应的模块说明**

第一项就是普通的傅立叶变换求解：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{X}^{(\mathrm{n}+1)}: x^{(n+1)}=F^{-1}\left(\frac{y+\rho^{(n+1)} F\left(x_{t}^{(n)}\right)}{F F_{p}^{T} F_{p} F^{T}+\rho^{(n+1)} I}\right)" border="0"/>

第二项作者用一个一阶梯度迭代的形式来求解，因此对应的网络形式也是多层：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{Z}^{(\mathrm{n}+1)}:\left\{\begin{array}{l}{u^{(n+1, k)}=\mu_{1}^{(n+1, k)} u^{(n+1, k-1)}+\mu_{2}^{(n+1, k)} x^{(n+1)}-c_{2}^{(n+1, k)}} \\ {c_{1}^{(n+1, k)}=w_{1}^{(n+1, k)} * u^{(n+1, k)}+b_{1}^{(n+1, k)}} \\ {h^{(n+1, k)}=S_{P L F}\left(c_{1}^{(n+1, k)} ;\left\{p_{i}, q_{i}^{(n+1, k)}\right\}_{i=1}^{N_{c}}\right)} \\ {c_{2}^{(n+1, k)}=w_{2}^{(n+1, k)} * h^{(n+1, k)}+b_{2}^{(n+1, k)}}\end{array}\right." border="0"/>

这里的 $w_1, w_2$ 都是卷积，$S_{PLF}$ 是激活函数，用分片线性函数表示（有很多个参数） 

第三项没什么变化：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{R}^{(\mathbf{n}+1)}: x_{t}^{(n+1)}=u^{(n+1)}+T^{(n+1)} \otimes\left(x^{(n+1)}-u^{(n+1)}\right)" border="0"/>

网络结构示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200208150453.png"/>

感觉和 ISTA-net 的方法没什么差别。

refine 部分的数学说明

<img src="http://latex.codecogs.com/svg.latex? T(u)=1-|c(p, q) s(p, q)|=1-\left|\frac{2 \sigma_{p q}+V}{\sigma_{p}^{2}+\sigma_{q}^{2}+V}\right|" border="0"/>

在计算 $T(u)$ 前，需要先对图像进行高斯模糊卷积，$p,q$ 分别表示的应该是原图像和高斯模糊后的图像的对应的同位置的 patch。这里的 $V$ 是参数。$\sigma_p,\sigma_q,\sigma_{pq}$ 则分别表示标准差和协方差。

### Bibtex:

```latex
@article{liu2019ifr,
  title={IFR-Net: Iterative Feature Refinement Network for Compressed Sensing MRI},
  author={Liu, Yiling and Liu, Qiegen and Zhang, Minghui and Yang, Qingxin and Wang, Shanshan and Liang, Dong},
  journal={IEEE Transactions on Computational Imaging},
  year={2019},
  publisher={IEEE}
}
```

