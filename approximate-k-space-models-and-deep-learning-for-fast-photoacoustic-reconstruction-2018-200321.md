### Paper:

Approximate k-space models and Deep Learning for fast photoacoustic reconstruction

### Author:

Andreas Hauptmann, Ben Cox, Felix Lucka, Nam Huynh, Marta Betcke, Paul Beard, and Simon Arridge

### Year:

2018

### Notes:

泛读。这篇文章研究的是 photoacoustic reconstruction，翻译是光声成像。和 MRI 有一点类似吧，总之也是一个线性系统。这篇文章用的方法总体来说可以看成 unroll 形式的网络，但是 DC 的部分和 CNN 的部分是结合起来的，这是这篇文章最特别的地方。具体而言，网络的输入不是 DC 的结果，而是 data fidelity 的梯度和当前图像，两者一起输入到网络中，输出是图像。另外梯度的计算也不是严格的 $A$ 的转置，而是伪逆。作者说这是估计。
$$
f_{k+1}=G_{\theta_{k}}\left(f_{k}, \nabla d\left(f_{k}, g\right)\right)
$$

$$
\nabla_{\mathcal{F}} d\left(f_{k}, g\right):=A_{\mathcal{F}}^{\dagger}\left(A_{\mathcal{F}} f_{k}-g\right) \approx \nabla d\left(f_{k}, g\right)
$$

网络结构也是类似于 U-net：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200321223125.png"/>

### Bibtex:

```latex
@inproceedings{hauptmann2018approximate,
  title={Approximate k-space models and deep learning for fast photoacoustic reconstruction},
  author={Hauptmann, Andreas and Cox, Ben and Lucka, Felix and Huynh, Nam and Betcke, Marta and Beard, Paul and Arridge, Simon},
  booktitle={International Workshop on Machine Learning for Medical Image Reconstruction},
  pages={103--111},
  year={2018},
  organization={Springer}
}
```

### 其他