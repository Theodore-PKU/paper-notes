### Paper:

Compressive Image Recovery Using Recurrent Generative Model

### Author:

Akshat Dave, Anil Kumar Vadathya, Kaushik Mitra

### Year:

2017

### Notes:

阅读时间：2020.01.10

精读。这篇文章考虑的是较一般的逆问题（图像信号重建）。作者的思路表述起来比较简单，不过论文中的一些看起来很简单的做法可能有一些有趣的地方。作者的想法是用 RIDE 作为图像的概率分布。RIDE 是一个借助于 LSTM 模型和 Mixture of Conditional Gaussian Scale Mixtures 的概率分布，作为图像的 prior。通过极大化后验概率实现信号重建，而极大化后验概率的方式是对信号 x 做梯度迭代。

使用 LSTM 的结构来刻画图像的概率分布，目前我没有看到其他的文章这么做。在论文中，作者还提到这个 LSTM 可以是2D空间上，考虑不同的方向。（我感觉 LSTM 模型不一定最合适，但考虑了马尔可夫性，一定程度有可以借鉴的地方）。

作者首先考虑了以左上角的马尔可夫性的概率写法：

<div align=center>
    <img src="http://latex.codecogs.com/svg.latex? p(\mathbf{x})=\prod_{i j} p\left(x_{i j} | \mathbf{x}_{<i j}, \boldsymbol{\theta}_{i j}\right)"  border="0"/>
</div>

马尔可夫性质和局部性相关，参数的共享则和平移不变性有关。在 Mixture of Conditional Gaussian Scale Mixtures (MCGSM) 模型中，可以写成更详细的：

<div align=center>
    <img src="http://latex.codecogs.com/svg.latex? p\left(x_{i j} | \mathbf{x}_{<i j}, \boldsymbol{\theta}\right)=\sum_{c, s} p\left(c, s | \mathbf{x}_{<i j}, \boldsymbol{\theta}\right) p\left(x_{i j} | \mathbf{x}_{<i j}, c, s, \boldsymbol{\theta}\right)" border="0">
</div>

其中，相关项定义为：

<div align=center>
    <img src="http://latex.codecogs.com/svg.latex? p\left(c, s | \mathbf{x}_{<i j}\right) \propto \exp \left(\eta_{c s}-0.5 * e^{\alpha_{c s}} \mathbf{x}_{<i j}^{T} \mathbf{K}_{c} \mathbf{x}_{<i j}\right)"  border="0"/>
</div>

<div align=center>
    <img src="http://latex.codecogs.com/svg.latex? p\left(x_{i j} | \mathbf{x}_{<i j}, c, s\right)=\mathcal{N}\left(x_{i j} ; \mathbf{a}_{c}^{T} \mathbf{x}_{<i j}, e^{-\alpha_{c s}}\right)"  border="0"/>
</div>

作者在这里表示 ，如果用Markov假设，可能不能包含更远处的信息.但是如果要包含更远的信息而不加以限制，就会导致参数量大增。因此在这引用的[27]考虑了两个方向的2d空间上的LSTM。感觉意思应该是说和马尔可夫性和两个方向有关。LSTM 似乎也就和平常的不一样。在这种情况下，图像的概率的写法中，参数还包括了LSTM 模型：

<div align=center>
    <img src="http://latex.codecogs.com/svg.latex? p(\mathbf{x})=\prod p\left(x_{i j} | \mathbf{h}_{i j}, \boldsymbol{\theta}\right), \mathbf{h}_{i j}=f\left(\mathbf{x}_{<i j}, \mathbf{h}_{i-1, j}, \mathbf{h}_{i, j-1}\right)"  border="0"/>
</div>

<div align=center>
    <img src="http://latex.codecogs.com/svg.latex? p\left(x_{i j} | \mathbf{h}_{i j}, \boldsymbol{\theta}\right)=\sum_{c, s} p\left(c, s | \mathbf{h}_{i j}, \boldsymbol{\theta}\right) p\left(x_{i j} | \mathbf{h}_{i j}, c, s, \boldsymbol{\theta}\right)"  border="0"/>
</div>

这里的 c, s 的具体含义不太清楚，需要看参考文献 28

在有了图像的概率分布之后（RIDE prior 是已经训练好的，也就是LSTM模型是另外训练，和后面的推导就没有关系了，所以作者在整篇文章中都没有提到这部分内容），作者用极大化后验概率（MAP）的方式来重建信号：

<div align=center>
    <img src="http://latex.codecogs.com/svg.latex? \hat{\mathbf{x}}=\arg \max _{\mathbf{x}} p(\mathbf{x} | \mathbf{y})=\arg \max _{\mathbf{x}} p(\mathbf{x}) p(\mathbf{y} | \mathbf{x})"  border="0"/>
</div>

在很多情况下，<img src="http://latex.codecogs.com/svg.latex? p(\mathbf{y} | \mathbf{x})"  border="0"/> 和噪声相关，因此是高斯的，可以写成 <img src="http://latex.codecogs.com/svg.latex? p(\mathbf{y} | \mathbf{x})\propto \exp (-\| \mathbf{y}-\left.A \mathbf{x} \|^{2} / \sigma^{2}\right)"  border="0"/>

为了计算 MAP，作者的方法是在取对数的情况下，用梯度法计算。对于 inpianting 和其他类型的任务，有不太一样的写法。作者在论文中给出：

一般情况下，

<div align=center>
    <img src="http://latex.codecogs.com/svg.latex? \hat{\mathbf{x}}_{t+1}=\hat{\mathbf{x}}_{t}+\eta \nabla_{\mathbf{x}} \log (p(\mathbf{x}) p(\mathbf{y} | \mathbf{x}))"  border="0"/>
</div>

<div align=center>
    <img src="http://latex.codecogs.com/svg.latex? \frac{\partial \log p(\mathbf{x})}{\partial x_{i j}}=\sum_{k \geq i, l \geq j} \frac{\partial \log p\left(x_{k l} | \mathbf{h}_{k l}, \boldsymbol{\theta}\right)}{\partial x_{i j}}"  border="0"/>
</div>

<div align=center>
    <img src="http://latex.codecogs.com/svg.latex? \nabla_{\mathbf{x}} \log p(\mathbf{y} | \mathbf{x}) \propto 2 A^{T}(\mathbf{y}-A \mathbf{x})"  border="0"/>
</div>

需要指出的是，在第一个式子中，这种写法有点像残差，当然要看成对输入的求导是可以的。

对于 inpainting，

<div align=center>
    <img src="http://latex.codecogs.com/svg.latex? \hat{\mathbf{x}}_{k}=\mathbf{x}_{k-1}+\eta \nabla_{\mathbf{x}_{k-1}} p(\mathbf{x})\\ \mathbf{x}_{k}=\hat{\mathbf{x}}_{k}-\Phi^{T}\left(\Phi \Phi^{T}\right)^{-1}\left(\Phi \hat{\mathbf{x}}_{k}-\mathbf{y}\right)"  border="0"/>
</div>

如果还要考虑噪声的存在，那么将 hard threshoding 改成 soft thresholding。

最后，给出作者在论文中提到的技巧。

1. 考虑不同方向的梯度。这个没太懂。
2. Entropy-based Thresholding。作者认为，这种重建方法有一个缺陷是在纹理的部分出现blur的情况，边缘处则比较。作者指出，posterior entropy 在边缘处比较低，在 flat 或纹理的地方高，所以为了避免因为 MAP 而blur，作者通过计算 posterior entropy，将较高值的像素的梯度置为0. posterior entropy 的定义为：

<div align=center>
    <img src="http://latex.codecogs.com/svg.latex? H(i, j)=-\sum_{c, s} p\left(c, s | \mathbf{x}_{<i j}, x_{i j}\right) \log \left(p\left(c, s | \mathbf{x}_{<i j}, x_{i j}\right)\right)"  border="0"/>
</div>

参考的文献：

1. [27] L. Theis and M. Bethge. Generative image modeling using spatial lstms. In Advances in Neural Information Processing Systems, pages 1927–1935, 2015. 这一篇讲的是 RIDE
2. [28] L. Theis, R. Hosseini, and M. Bethge. Mixtures of conditional gaussian scale mixtures applied to multiscale image representations. PloS one, 7(7):e39857, 2012. 这一篇讲的是 Mixture of Conditional Gaussian Scale Mixtures

### Bibtex:

```latex
@inproceedings{dave2017compressive,
  title={Compressive image recovery using recurrent generative model},
  author={Dave, Akshat and Kumar, Anil and Mitra, Kaushik and others},
  booktitle={2017 IEEE International Conference on Image Processing (ICIP)},
  pages={1702--1706},
  year={2017},
  organization={IEEE}
}
```