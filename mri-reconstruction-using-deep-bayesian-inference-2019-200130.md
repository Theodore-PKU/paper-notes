### Paper:

MRI Reconstruction Using Deep Bayesian Inference

### Author:

GuanXiong Lu, Na Zhao, Wenhao Jiang, Peng Cao

### Year:

2019

### Notes:

阅读时间：2020.01.30

泛读。这篇文章研究的是 MRI 的重建，用的方法比较少见，通过贝叶斯推断来重建图像。具体而言，用一个神经网络来计算图像信号 $x$ 的先验概率，通过梯度迭代的方法，在满足 data fidelity 的条件下，极大化后验似然概率（关于 x 优化）。作者使用的 pixelCNN++ 这一网络来表示先验概率（这一部分没有完全看懂），梯度迭代算法比较朴实，通过这种方式得到的重建图像效果感觉很好，不过计算量就比较大。

贝叶斯公式：（这里的 $g(x)$ 表示先验概率，后文中表示网络）

<img src="http://latex.codecogs.com/svg.latex? \hat{\boldsymbol{x}}_{\mathrm{MAP}}(\boldsymbol{y})=\underset{\boldsymbol{x}}{\arg \max } f(\boldsymbol{x} | \boldsymbol{y})=\underset{\boldsymbol{x}}{\arg \max } f(\boldsymbol{y} | \boldsymbol{x}) g(\boldsymbol{x})" border="0"/>

作者采用的先验概率的表达是为：

<img src="http://latex.codecogs.com/svg.latex? P(\nu ; \pi, \mu, s)=\sum_{i=1}^{K} \pi_{i}\left[\sigma\left(\nu+0.5-\mu_{i}\right)/s_{i}-\sigma\left(\nu-0.5-\mu_{i}\right)/s_{i}\right]" border="0"/>

作者说这里的 $\mu, s$ 分别表示均值和方差，$\sigma$ 表示 logistic sigmoid function，不知道要怎么算。然后考虑一个条件概率：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} p\left(x_{i, j} | C_{i, j}\right)=p\left(\operatorname{Re}&\left(x_{i, j}\right), \operatorname{Im}\left(x_{i, j}\right) | C_{i, j}\right)= P\left(\operatorname{Re}\left(x_{i, j}\right) | \mu_{\mathrm{Re}}\left(C_{i, j}\right), s_{\mathrm{Re}}\left(C_{i j}\right)\right) \times  P\left(\operatorname{Im}\left(x_{i, j}\right) | \mu_{\mathrm{Im}}\left(C_{i, j}, \operatorname{Re}\left(x_{i, j}\right)\right), s_{\operatorname{Im}}\left(C_{i, j}\right)\right) \\ &\mu_{\mathrm{Im}}\left(C_{i, j}, \operatorname{Re}\left(x_{i, j}\right)\right)=\mu_{\mathrm{Im}}\left(C_{i, j}\right)+\alpha\left(C_{i, j}\right) \operatorname{Re}\left(x_{i, j}\right) \end{aligned}" border="0"/>

这种表达的含义是每个像素点的概率是一个条件概率，$C_{i,j}$ 表示当前行左边和上面行的所有像素（上式的 $\mu, s$ 好像表示的还是均值和方差）。因此整体的概率就可以写成

<img src="http://latex.codecogs.com/svg.latex? p(\boldsymbol{x} ; \boldsymbol{\pi}, \boldsymbol{\mu}, \boldsymbol{s}, \boldsymbol{\alpha})=p\left(x^{(1)}\right) \prod_{i=2}^{n^{2}} p\left(x^{(i)} | x^{(1)}, \ldots, x^{(i-1)}\right)" border="0"/>

作者说用似然函数作为网络训练的损失函数，但是损失函数的具体形式是什么，包括如何计算都没懂。（网络输出是什么没有搞清楚，pixelCNN++ 可以看一下 Tim Salimans, Andrej Karpathy, Xi Chen, and Diederik P Kingma. Pixelcnn++: Improving the pixelcnn with discretized logistic mixture likelihood and other modiﬁcations. arXiv preprint arXiv:1701.05517, 2017）。假设训练好了网络，那么贝叶斯的计算就是：

<img src="http://latex.codecogs.com/svg.latex? \hat{\boldsymbol{x}}_{\mathrm{MAP}}(\boldsymbol{y})=\underset{\boldsymbol{x}}{\arg \max } \log p(\boldsymbol{x} | \mathrm{NET}(\boldsymbol{x}, \hat{\Theta})) \quad \text { s.t. } \quad \boldsymbol{y}=\mathbf{A} \boldsymbol{x}+\varepsilon" border="0"/>

迭代过程为：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-30%E4%B8%8B%E5%8D%889.49.12.png" width="90%"/>

关于训练的一些细节，因为 pixelCNN++的计算量较大，所以作者使用了 128x128 大小的复数值图像（作者通过选择 full kspace 中心的 128x128 区域来计算出图像）作为网络的输入，在重建时则可以使用任意的尺寸。

pixelCNN++ 的网络结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-30%E4%B8%8B%E5%8D%889.51.23.png"/>

> 作者的思路最重要的启发在于将 prior 和 data fidelity 分开，这样可以有很强的灵活性，适用于不同的采样率，各种设定都可以。最大的问题在于迭代求解的过程非常慢，所以结合 unroll 的形式本身就是一个很好的出发点。

### Bibtex:

```latex
@article{luo2019mri,
  title={MRI Reconstruction Using Deep Bayesian Inference},
  author={Luo, GuanXiong and Zhao, Na and Jiang, Wenhao and Cao, Peng},
  journal={arXiv preprint arXiv:1909.01127},
  year={2019}
}
```

