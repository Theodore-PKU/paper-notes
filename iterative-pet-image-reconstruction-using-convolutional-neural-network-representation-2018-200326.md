### Paper:

Iterative PET Image Reconstruction Using Convolutional Neural Network Representation

### Author:

Kuang Gong, Jiahui Guan, Kyungsang Kim, Xuezhu Zhang, Georges El Fakhri, Jinyi Qi and Quanzheng Li

### Year:

2018

### Notes:

泛读。这篇文章研究的是 PET 的重建，使用的方法是神经网络和迭代算法结合的方式。方法归类应该算生成模型这一类。作者把 PET 重建图像限制在一个 U-net 的输出，这作为目标函数的约束条件，目标函数是对数似然函数。求解时用的是 ADMM 算法。U-net 事先需要训练，类似于去噪或者后处理的网络，不过输入是多张相邻图像。比较奇怪的地方在于，对于网络输出的约束条件，作者对于网络输入并没有限制，也就是说虽然网络是训练好的，但是输入仅仅是一个初始的变量，是优化的核心变量。如果把对数似然函数看成是 DC，那么这个方法很接近于 GAN 搜索的方法，只不过使用的网络不是 GAN，而是一个类似去噪的网络。但是从实际的效果上看，又不是去噪的作用，因为输入是可变的，作者仅仅是希望 x 是网络的输出。
$$
\begin{aligned}
&\max \quad L(\boldsymbol{y} | \boldsymbol{x})\\
&\text { s.t. } \quad \boldsymbol{x}=f(\boldsymbol{\alpha})
\end{aligned}
$$
这里的 $L$ 表示对数似然函数，$f$ 表示网络，$\alpha$ 表示输入。ADMM 展开是
$$
\begin{aligned}
\boldsymbol{x}^{n+1} &=\underset{\boldsymbol{x}}{\arg \max } L(\boldsymbol{y} | \boldsymbol{x})-\frac{\rho}{2}\left\|\boldsymbol{x}-f\left(\boldsymbol{\alpha}^{n}\right)+\boldsymbol{\mu}^{n}\right\|^{2} \\
\boldsymbol{\alpha}^{n+1} &=\underset{\boldsymbol{\alpha}}{\arg \min }\left\|f(\boldsymbol{\alpha})-\left(\boldsymbol{x}^{n+1}+\boldsymbol{\mu}^{n}\right)\right\|^{2} \\
\boldsymbol{\mu}^{n+1} &=\boldsymbol{\mu}^{n}+\boldsymbol{x}^{n+1}-f\left(\boldsymbol{\alpha}^{n+1}\right)
\end{aligned}
$$
实际上，第一个子问题还是使用了 surrogate function 的方法替代求解。第二个子问题用了一阶梯度的方法 Nesterov momentum method 对输入 $\alpha$ 优化。

有一个疑问是关于对数似然函数：
$$
L(\boldsymbol{y} | \boldsymbol{x})=\sum_{i=1}^{M} y_{i} \log \bar{y}_{i}-\bar{y}_{i}-\log y_{i} !
$$
原始的模型是：
$$
\overline{\boldsymbol{y}}=\boldsymbol{P} \boldsymbol{x}+\boldsymbol{s}+\boldsymbol{r}
$$
$s, r$ 应该如何计算？

网络结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200326223131.png"/>

另外，这篇文章还有关于如何获得模拟数据的较为详细的讨论。

### Bibtex:

```latex
@article{gong2018iterative,
  title={Iterative PET image reconstruction using convolutional neural network representation},
  author={Gong, Kuang and Guan, Jiahui and Kim, Kyungsang and Zhang, Xuezhu and Yang, Jaewon and Seo, Youngho and El Fakhri, Georges and Qi, Jinyi and Li, Quanzheng},
  journal={IEEE transactions on medical imaging},
  volume={38},
  number={3},
  pages={675--685},
  year={2018},
  publisher={IEEE}
}
```

### 其他

