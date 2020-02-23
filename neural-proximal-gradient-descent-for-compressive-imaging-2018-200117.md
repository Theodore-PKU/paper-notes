### Paper:

Neural Proximal Gradient Descent for Compressive Imaging

### Author:

Morteza Mardani, Qingyun Sun, Shreyas Vasawanala, Vardan Papyan, Hatef Monajemi, John Pauly, and David Donoho

### Year:

2018

### Notes:

阅读日期：2020.01.17

精读。（但感觉这篇文章比较水）。这篇文章研究的是压缩感知的重建，主要是 MRI。作者的想法是对 proximal 算法展开。这个算法对于逆问题都是有效的。proximal 算法分为两步，一步是 data fidelity，一步是 proximal 算子（由正则项决定）。作者的方法就是将 proximal 算子用 res-block 的网络来表示，同时 data fidelity 的步长是可学习的参数。proximal 算子的 res block 的数量可以选择多个，也可选择单个。作者在实验部分探究了不同的选择和迭代次数的效果。整体上就是一个 recursive 的框架。

proximal 迭代：

<img src="http://latex.codecogs.com/svg.latex? x_{t+1}=\mathcal{P}_{\psi}\left(x_{t}-\alpha \nabla \frac{1}{2}\left\|y-\Phi x_{t}\right\|^{2}\right)=\mathcal{P}_{\psi}\left(x_{t}+\alpha\Phi^{\mathrm{H}}\left(y-\Phi x_{t}\right)\right)" border="0"/>

展开为网络后的问题：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned}{\min _{\mathcal{W}, \alpha} \beta \sum_{i=1}^{N} \ell\left(x_{i}, x_{i}^{T}\right)+(1-\beta) \sum_{i=1}^{N} \sum_{t=1}^{T}\left\|y_{i}-\Phi x_{i}^{t}\right\|^{2}} \\ {\text { s.t. }  \hat{x}_{i}^{t}=\left(\mathcal{P}_{\psi} \circ g\right)^{t}\left(\Phi^{\mathrm{H}} y_{i}\right), \forall i \in[N], t \in[T]}\end{aligned}" border="0"/>

res block 网络结构：we adopted a ResNet with a variable number of residual blocks (RB). Each RB consisted of two convolutional layers with 3 × 3 kernels and a ﬁxed number of 128 feature maps, respectively, that were followed by batch normalization (BN) and ReLU activation. We followed these by three simple convolutional layers with 1 × 1 kernels, where the ﬁrst two layers undergo ReLU activation.

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-17%E4%B8%8A%E5%8D%8811.30.59.png"/>

> 这里有一个想法：恢复信号的时候是否可以逐步恢复，比如每次都增加一些 kspace （从低频开始）使其不断满足，也就是说有很多个中间值和损失函数。

### Bibtex:

```latex
@inproceedings{mardani2018neural,
  title={Neural proximal gradient descent for compressive imaging},
  author={Mardani, Morteza and Sun, Qingyun and Donoho, David and Papyan, Vardan and Monajemi, Hatef and Vasanawala, Shreyas and Pauly, John},
  booktitle={Advances in Neural Information Processing Systems},
  pages={9573--9583},
  year={2018}
}
```

