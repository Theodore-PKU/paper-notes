### Paper:

Penalized PET reconstruction using deep learning prior and local linear fitting

### Author:

Kyungsang Kim, Dufan Wu, and Kuang Gong, Joyita Dutta, Jong Hoon Kim, Young Don Son and Hang Keun Kim, Georges El Fakhri and Quanzheng Li

### Year:

2018

### Notes:

泛读。这篇文章研究的 PET 重建。使用的方法总体上说是去噪网络和迭代算法的结合，创新点是增加了一个 local linear fitting。去噪网络和 local linear fitting 结合起来作为目标函数的正则项，目标函数的另一项是对数似然函数。在 PET 重建中，对数似然函数和 DC 是相关的。所以可以看成是 DC 项。作者首先将目标函数进行了 splitting 和放缩，转换出新的目标函数，然后用 ADMM 算法求解。去噪网络是事先训练好的。local linear fitting 的目的是为了减少去噪网络中输入的噪声水平变化的问题。一般来说，如果只有去噪网络，训练的时候可能是固定的噪声水平，但在迭代过程中，噪声水平是可变的，所以会出现训练和测试的不一致，另外对于 PET 而言，噪声不稳定。总结起来，我认为可以当成 unroll 类型的方法，不过网络是事先训练好的，LLF 的目的是解决噪声的 trick。另外，这篇文章使用的优化目标函数的转换值得注意。去噪网络是 DnCNN，这倒没什么特别的地方（除了没有使用 residual learning 之外）。

去噪网络的训练损失函数：
$$
l(w)=\frac{1}{2 N} \sum_{i=1}^{N}\left\|f\left(x_{i} ; w\right)-x_{i}^{*}\right\|_{2}^{2}+\lambda\|w\|_{2}^{2}
$$
首先说明一下 LLF 的含义：
$$
\hat{x}_{k}=q_{i} x_{k}^{D}+b_{i}, \forall k \in p_{i}
$$
可以理解成，每个 patch 的像素，假设 DnCNN 的输入和输出之间存在一个固定的线性变换，对于同一个 patch 的所有像素使用相同的线性变换。LLF 的目的是估计出线性变换的两个参数后，用线性变换的结果替代 DnCNN 的输出。所以 DnCNN 的实际作用是给出一个 target，来获得线性变换的参数 $q_i, b_i$. 这里的 $k$ 表示的是 patch $p_i$ 的像素。

估计的目标函数是：
$$
E\left(q_{i}, b_{i}\right)=\sum_{k \in p_{i}}\left(\left(q_{i} x_{k}^{D}+b_{i}-x_{k}\right)^{2}+\epsilon q_{i}^{2}\right)
$$
这有闭式解：
$$
\begin{aligned}
&q_{i}^{(n+1)}=\frac{\frac{1}{N_{p}} \sum_{k \in p_{i}} x_{k}^{D} x_{k}-b_{i}^{(n)} \bar{x}_{i}^{D}}{\frac{1}{N_{p}} \sum_{k \in p_{i}} x_{k}^{D} x_{k}^{D}+\epsilon}\\
&b_{i}^{(n+1)}=\bar{x}_{i}-q_{i}^{(n+1)} \bar{x}_{i}^{D}
\end{aligned}
$$
最终的结果表达为：
$$
\widehat{x}_{i}=q_{i} x_{i}^{D}+b_{i}
$$
下面主要讨论目标函数的变化。

物理模型：
$$
y_{i} \sim \text { Poisson }\left\{[A x]_{i}+r_{i}\right\}
$$
$r_i$ 表示的是随机光子事件吧。。。。

原始目标函数是
$$
\Psi_{o}(x)=L(x)+R(x) \\ =\sum_{i=1}^{N_m} [A x]_{i}+r_{i}-y_{i} \log \left([A x]_{i}+r_{i}\right) + \frac{\beta}{2}\left\|x-q \odot f_{w}(x)-b\right\|_{2}^{2}
$$
分离变量：
$$
\Psi_{o}\left(x, x^{D}, \eta\right)=L(x)+R\left(x, x^{D}\right)+D\left(x, x^{D}, \eta\right) \\= L(x)+\frac{\beta}{2}\left\|x-q \odot x^{D}-b\right\|_{2} + \frac{\gamma}{2}\left\|x^{D}-f_{w}(x)-\eta\right\|_{2}^{2}
$$
放缩：
$$
L(x) \leq \phi_{L, j}^{(n)}\left(x_{j}\right) \triangleq \sum_{i=1}^{M} g_{i j} p_{i}^{(n)}\left(\frac{a_{i j}}{g_{i j}}\left(x_{j}-x_{j}^{(n)}\right)+\left[A x^{(n)}\right]_{i}\right)
\\R\left(x, x^{D}\right) \leq \frac{\beta}{4}\left(\left(2 q \odot x^{D}+2 b-z^{(n)}\right)^{2}+\left(z^{(n)}-2 x\right)^{2}\right)
$$
这里的定义比较复杂，不详细写了。最后给出的算法参见论文。

### Bibtex:

```latex
@article{kim2018penalized,
  title={Penalized PET reconstruction using deep learning prior and local linear fitting},
  author={Kim, Kyungsang and Wu, Dufan and Gong, Kuang and Dutta, Joyita and Kim, Jong Hoon and Son, Young Don and Kim, Hang Keun and El Fakhri, Georges and Li, Quanzheng},
  journal={IEEE transactions on medical imaging},
  volume={37},
  number={6},
  pages={1478--1487},
  year={2018},
  publisher={IEEE}
}
```

### 其他

Gong K, Guan J, Kim K, Zhang X, Fakhri GE, Qi J, and Li Q, “Iterative PET Image Reconstruction Using Convolutional Neural Network Representation,” arXiv preprint arXiv: 1710.03344, 2017.