# Uncertainty Quantiﬁcation in Deep MRI Reconstruction

[TOC]

### 信息

Authors: Vineet Edupuganti, Morteza Mardani, Shreyas Vasanawala, and John Pauly

Stanford University

IEEE Transactions on Medical Imaging

```latex
@article{edupuganti2020uncertainty,
  title={Uncertainty quantification in deep mri reconstruction},
  author={Edupuganti, Vineet and Mardani, Morteza and Vasanawala, Shreyas and Pauly, John},
  journal={IEEE Transactions on Medical Imaging},
  year={2020},
  publisher={IEEE}
}
```

引用：3

[Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/uncertainty-quantiﬁcation-in-deep-mri-reconstruction.pdf)



### 代码

https://github.com/MortezaMardani/GAN-Hallucination/tree/VAE-GAN



### 概括

这篇文章提出使用 VAE 结构的网络实现 MRI 重建。利用 VAE 隐变量的随机性，作为重建结果随机性的表示方式，并通过蒙特卡洛采样和 SURE 方法计算重建结果的 risk。作者对这种 VAE 网络做了两个额外的测试。第一个是增加对抗损失函数，第二个是 recurrent 形式的多个 VAE 网络。在这两种网络设定下计算生成结果的 risk。

VAE 网络的简单说明：实际上的网络基本结构是一个 U-Net，但是在 bottleneck 部分，通过全连接层得到隐变量，然后再用全连接层得到 decoder 的初始输入。可以认为是在 U-Net 这样的 baseline 上增加了 VAE 的组建。

蒙特卡洛和 SURE 方法：蒙特卡洛就是在对 latent variable 做多次采样，得到多个重建结果。利用这些重建结果和 SURE 方法，就可以得到 risk 的计算。SURE 的目的是在没有 ground truth 的情况下也可以方便地计算 risk。

实验的结论是：GAN 会增加 risk，但是图像图像会更锐利，recurrent 结构会减少 risk，两者结合的效果是最好的。

这篇文章最值得反思的一点在于：隐变量的后验分布是否能称为重建结果好坏的度量。损失函数是否会影响隐变量的后验分布的估计。



### 方法

#### 网络结构

VAE 网络的基本结构是一个 U-Net，使用了 skip connection，但是图示中没有显示出来。隐变量利用了全连接层，这在代码中得到了验证。通道数很多：128, 256, 512, 1024，5x5 卷积，ReLU 激活函数和 BN 层等。

![屏幕快照 2020-12-21 下午2.10.23](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-21 下午2.10.23.png)

损失函数就是 reconstruction loss 和 KL 项：
$$
\begin{equation}
\min _{h} \mathbb{E}_{x, y}\left[\left\|\hat{x}-x_{0}\right\|_{2}^{2}\right]+\eta K L\left(\mathcal{N}\left(\mu_{z}, \sigma_{z}\right) \| \mathcal{N}(0,1)\right)
\end{equation}
$$
网络的输入是 zf 重建的结果，实际上作者还增加了一个 compensation 的预处理过程，其目的是尽量满足 SURE 的残差高斯分布特性。

最后的输出，作者加了一个 Data consistency 层。



#### 蒙特卡洛模拟

蒙特卡洛的采样很简单，首先对 zf 重建的网络输入得到隐变量的后验分布，然后对后验分布进行多次采样，通过 decoder 得到不同的重建结果。最后取平均。得到的 map 就是多次采样结果和平均值之间的 variance：
$$
\begin{equation}
\operatorname{map}:=\frac{1}{k} \sum_{i=1}^{k}\left(\hat{x}_{i}-\hat{x}\right)^{2}
\end{equation}
$$


#### SURE

在没有真实图像的情况下，要计算 risk，可以采用 SURE 方法。理论推导如下：

首先假设 zf 图像和真实图像之间的残差是高斯的。那么有推导
$$
\begin{equation}
\begin{aligned}
\mathbb{E}\left[\left\|\hat{x}-x_{0}\right\|^{2}\right] &=\mathbb{E}\left[\left\|x_{0}-x_{z f}+x_{z f}-\hat{x}\right\|^{2}\right] \\
&=-n \sigma^{2}+\mathbb{E}\left[\left\|x_{z f}-\hat{x}\right\|^{2}\right]+2 \operatorname{Cov}\left(x_{z f}, \hat{x}\right)
\end{aligned}
\end{equation}
$$
而 SURE 的定义是：
$$
\begin{equation}
S U R E=-n \sigma^{2}+\underbrace{\left\|\hat{x}-x_{z f}\right\|^{2}}_{\text {RSS }}+\sigma^{2} \underbrace{\operatorname{tr}\left(\frac{\partial \hat{x}}{\partial x_{z f}}\right)}_{\text {DOF }}
\end{equation}
$$
SURE 是无偏的，满足 $MSE = \mathbb{E}[SURE]$.

$\sigma$ 的估计是 $\begin{equation}
\sigma^{2}=\left\|\hat{x}-x_{z f}\right\|^{2} / n
\end{equation}$，所以和第一项抵消。

DOF 的实际计算通过：
$$
\begin{equation}
\operatorname{tr}\{J\}=\lim _{\epsilon \rightarrow 0} \mathbb{E}_{b \sim \mathcal{N}(0,1)}\left[b^{T}\left(h\left(x_{z f}+\epsilon b\right)-h\left(x_{z f}\right)\right) \epsilon^{-1}\right]
\end{equation}
$$

$$
\begin{equation}
\operatorname{tr}\{J\} \approx b^{T}\left(h\left(x_{z f}+\epsilon b\right)-h\left(x_{z f}\right)\right) \epsilon^{-1}
\end{equation}
$$

$\epsilon$ 是一个小量，作者的选取方式是最大像素值 / 10^3



#### 其他

对抗损失函数的形式是：
$$
\begin{equation}
\min _{D} \mathbb{E}_{x}\left[(1-\mathcal{D}(x))^{2}\right]+\mathbb{E}_{y}\left[(\mathcal{D}(\hat{x}))^{2}\right]
\end{equation}
$$
判别器的网络也不是很复杂：8 层的 CNN，使用了 3x3 卷积，1x1 卷积和 ReLU，BN

在 recurrent 结构中，DC 层是连接不同 VAE 的中间层。

compensation：为了保证 zf 重建和真实图像直接是高斯分布的残差，作者使用了下述公式：
$$
\begin{equation}
\tilde{x}_{z f}=x_{0}+\underbrace{\left(F^{-1} D^{-1} \Omega F-I\right) x_{0}}_{:=r}
\end{equation}
$$
这的 $D$ 是一个表示采样概率的矩阵。和采样方式有关。如果这个残差不是高斯的，就需要使用更一般化的 SURE。作者在论文中说如果是 uniform sampling 就不能用这篇文章的 SURE。

这篇文章的采样方式 radial view ordering，这样可以很好地保证低频信息留下来。

数据集的分辨率是 320x256，只考虑的单线圈数据，用双通道表示复数。



### 实验结果

作者的实验主要是在 VAE 重建网络的基础上，增加了对抗损失函数和 recurrent 结构，并且评估用提出的方法计算的 risk 的差异。

作者发现使用对抗损失函数，会引入更多的像素级别的 uncertainty，cascaded 结构的网络会减少 risk，SURE 可以很好地用于估计 MSE 并且是一个合适的 risk 评估工具。

对抗损失函数在不同的 weight 下的 uncertainty 的评估：

![屏幕快照 2020-12-21 下午2.07.07](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-21 下午2.07.07.png)

recurrent 结构的 risk 评估

![屏幕快照 2020-12-21 下午2.08.25](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-21 下午2.08.25.png)

作者也实验验证了 SURE 的计算结果和真实的 MSE 的结果是比较正相关的。因此可以用于衡量 risk



### 反思

说实话，我觉得这篇文章有一些问题，核心就是重建的 risk，或者说 variance 应该用什么来衡量才是最合适的。突然意识到在 VAE 模型中，后验分布在理论上应该和输入有关，而不应该和模型有关。我们的目的使用模型更好地计算出后验分布。如果在不同的损失函数下，会得到不同的后验分布，那么这个后验分布和理论还是吻合的吗？当然最终重建的结果在不同的方法中，可能具有不同的置信程度，但是在理论上，这个 risk 的下界是由 input 本身决定的，和模型应该没有关系。

我觉得现在应该仔细思考隐变量的后验分布的含义是什么。隐变量的含义又是什么？