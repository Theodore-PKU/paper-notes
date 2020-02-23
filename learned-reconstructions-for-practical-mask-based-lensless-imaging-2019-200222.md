### Paper:

Learned reconstructions for practical mask-based lensless imaging

### Author:

Kristina Monakhova, Joshua Yurtsever, Grace, Kuo, Nick, Antipa, Kyrollos Yanny, Laura Waller

### Year:

2019

### Notes:

阅读日期：2020.02.22

泛读。这篇文章也非常简单，研究的是 lensless imaging，物理原理就不管了，总之仍是一个线性系统。作者使用的方法还是展开 admm 算法，提出了三种不同的网络，分别对应不同的训练参数。这三个网络的参数数量逐渐增加。第一种是将超参数变成可学习。第二种是加了一个CNN，替代稀疏变换，第三个是在最后一步加了一个去噪网络的CNN。没有什么参考性。

目标函数：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \hat{\mathbf{x}}=& \underset{w \geq 0, u, v}{\arg \min } \frac{1}{2}\|\mathbf{b}-\mathbf{C} v\|_{2}^{2}+\tau\|u\|_{1} \\ & \text { s.t. } v=\mathbf{H} \mathbf{x}, u=\Psi_{\mathbf{X}, W}=\mathbf{x} \end{aligned}" border="0"/>

三种不同的网络就忽略了，因为太简单了。

### Bibtex:

```latex
@article{monakhova2019learned,
  title={Learned reconstructions for practical mask-based lensless imaging},
  author={Monakhova, Kristina and Yurtsever, Joshua and Kuo, Grace and Antipa, Nick and Yanny, Kyrollos and Waller, Laura},
  journal={Optics express},
  volume={27},
  number={20},
  pages={28075--28090},
  year={2019},
  publisher={Optical Society of America}
}
```

