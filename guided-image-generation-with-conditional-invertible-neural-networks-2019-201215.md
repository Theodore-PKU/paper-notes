# Guided image generation with conditional invertible neural networks

[TOC]

### 信息

Authors: Lynton Ardizzone, Carsten Lüth, Jakob Kruse, Carsten Rother, Ullrich Köthe

Visual Learning Lab Heidelberg

Year 2019

```latex
@article{ardizzone2019guided,
  title={Guided image generation with conditional invertible neural networks},
  author={Ardizzone, Lynton and L{\"u}th, Carsten and Kruse, Jakob and Rother, Carsten and K{\"o}the, Ullrich},
  journal={arXiv preprint arXiv:1907.02392},
  year={2019}
}
```

引用 30+

[Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/guided-image-generation-with-conditional-invertible-neural-networks.pdf>)

### 概括

这篇文章研究的是 conditional flow-based model。基本模型是 realNVP，但是加入了 condition 的内容。和 SRflow 也非常类似。在这篇文章中，作者考虑了两个任务，一个是 MNIST 的条件生成，一个是自然图像的 colorization，并且也研究了一些和数据操纵有关的内容（利用隐变量）。

就 condtional flow-based model 而言，和 SRflow 等模型的做法都是非常类似的（应该说 SRflow 参考了这些模型的做法）。都是通过一个额外的神经网络，对 condition 进行信息提取，加入到 affine coupling layer 当中去。这篇文章比较特别的地方，在于网络的细节设计（用了一些看起来不错的技巧）、噪声数据增广 conditional 的信息提取、MNIST 生成中的数据操纵、以及隐变量的 split out 方式（用了 Haar 小波实现下采样）。

关于实验，作者做了一些消融实验来验证某些设计的有效性。其中，数据增广非常重要。



### Code

http://github.com/VLL-HD/FrEIA



### 方法

#### 一些评述

作者认为，在 cVAE 中，decoder 的高斯分布的假设是不合理的。（但是如果不用高斯分布呢？）不过在 flow-based model 中确实很好地避免了对最终输出的建模的假设。

对于 flow-based model，作者概括为通过训练，训练样本的似然函数在隐变量空间中得到极大化。在 flow-based model 中，因为 $\mathbf{x}$ 和 $\mathbf{z}$ 一一对应了，所以不需要考虑 $\mathbf{z}$ 关于 $\mathbf{x}$ 的后验分布。这似乎很合理。

在有的论文中，采用了极大似然函数和对抗损失两种 loss，但是作者认为，这是没有必要的，只需要似然函数就可以了。

（作者认为他们是第一个将 couping layer 用于 conditional 生成模型的）

#### 理论

基本模型仍然是：
$$
p_{X}(\mathbf{x} ; \mathbf{c}, \theta)=p_{Z}(f(\mathbf{x} ; \mathbf{c}, \theta))\left|\operatorname{det}\left(\frac{\partial f}{\partial \mathbf{x}}\right)\right|
$$
coupling layer 是：
$$
\begin{array}{l}
\mathbf{v}_{1}=\mathbf{u}_{1} \odot \exp \left(s_{1}\left(\mathbf{u}_{2}, \mathbf{c}\right)\right)+t_{1}\left(\mathbf{u}_{2},  \mathbf{c}\right) \\
\mathbf{v}_{2}=\mathbf{u}_{2} \odot \exp \left(s_{2}\left(\mathbf{v}_{1},  \mathbf{c}\right)\right)+t_{2}\left(\mathbf{v}_{1},  \mathbf{c}\right)
\end{array}
$$

$$
\begin{array}{l}
\mathbf{u}_{2}=\left(\mathbf{v}_{2}-t_{2}\left(\mathbf{v}_{1}, \mathbf{c}\right)\right) \oslash \exp \left(s_{2}\left(\mathbf{v}_{1}, \mathbf{c}
\right)\right) \\
\mathbf{u}_{1}=\left(\mathbf{v}_{1}-t_{1}\left(\mathbf{u}_{2}, \mathbf{c}\right)\right) \oslash \exp \left(s_{1}\left(\mathbf{u}_{2}, \mathbf{c}\right)\right)
\end{array}
$$

损失函数，作者从 $\theta$ 的高斯分布假设出发给出了一个带有 L2 正则化的损失函数（也就是网络参数有正则项），前一项则和通常的方法一样：
$$
\mathcal{L}=\mathbb{E}_{i}\left[\frac{\left\|f\left(\mathbf{x}_{i} ; \mathbf{c}_{i}, \theta\right)\right\|_{2}^{2}}{2}-\log \left|J_{i}\right|\right]+\tau\|\theta\|_{2}^{2}
$$
在这篇文章中，和 SRflow 一样，隐变量空间使用的分布和条件无关，所以生成的时候是 $\mathbf{z} \sim p_Z(\mathbf{z}), \mathbf{x}_{gen} = g\left(\mathbf{z} ; \mathbf{c}, \hat{\theta}_{\mathrm{ML}}\right)$

关于condition 的特征提取，作者认为这是非常有必要的，这样可以避免每次传入 $s, t$ 的时候重复的特征提取。因此实际传入的不是 $\mathbf{c}$ 而是 $\tilde{\mathbf{c}} = h(\mathbf{c})$

#### 其他网络设计

在 $\mathbf{x}$ 中加入噪声，达到数据增广的效果。

使用 soft clamping 函数实现对 scale 参数的控制：
$$
s_{\text {clamp }}=\frac{2 \alpha}{\pi} \arctan \left(\frac{s}{\alpha}\right)
$$
论文中 $\alpha = 1.9$. 这个设计和 NVAE 非常像，不过使用的函数不同。

初始化的方法：大部分参数采用Xavier initialization，但是 scale 和 bias 网络的最后一层使用的初始参数是 0，这样保证最开始是一个恒同变换。

channel 重排：这篇文章采用的重排方式类似于 glow 模型，使用了 1x1 卷积。但是参数是固定的，在训练之初，选择一个随机的正交矩阵。

Haar 小波下采样：作者认为这样处理某种程度上也是一种 permutation。

单独一个 conditional affine coupling layer 示意图：

![屏幕快照 2020-12-15 下午3.48.50](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-15 下午3.48.50.png)

#### MNIST 生成

在这个任务中，condition 是表示数字的 one-hot。没有使用特征提取的网络，而是直接将 one-hot 放入输入中。这样做的原因是作者的 scale，bias 网络都是全连接网络，因此可行。而且网络没有降采样，所以 input 和 隐变量的分辨率是一样的。

![屏幕快照 2020-12-15 下午7.15.32](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-15 下午7.15.32.png)

#### colorization

在这个任务中，保留了多尺度、降采样的网络结构。特征提取网络是一个预训练好的 VGG 网络，预训练的内容是从亮度图像（这里不是 RGB 图像，而是 LAB 图像）预测颜色。作者设计了一个多头结构，在信息提取的时候，每次参与 affine coupling layer 计算的特征提取都不一样。可以理解成一个共同的特征提取之后还有一个特征提取（这个也是网络，是一个 5 层的卷积网络，和具体所需要的分辨率有关）

![屏幕快照 2020-12-15 下午7.18.53](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-15 下午7.18.53.png)

这个图示里的通道数感觉有一些问题。

### 实验

#### MNIST 生成

这个任务中的输入 $\mathbf{x}$ 的噪声比较小。

作者研究了隐变量对生成效果的影响。首先，在训练好模型之后，作者用测试集，获得了隐变量，然后做 PCA，选择其中的主要成分。通过控制主成分的值来判断对与生成效果的影响。作者发现它们控制的是图像的不同内容。这里有一个问题，按理隐变量是正态分布，PCA 对正态分布的数据有用吗？或者说事实上，训练出来的隐变量不一定是正态分布？目前我对于隐变量的分布是否保持正态性是存疑的。

![屏幕快照 2020-12-15 下午7.27.58](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-15 下午7.27.58.png)

其次，作者发现隐变量 $\mathbf{z}$ 控制的是 style，相同的隐变量，不同的 condition，可以生成风格相同但是不同的数字。类似的，从某个图像获取对应的隐变量后，生成其他数字的风格和原始图像是接近的。

![屏幕快照 2020-12-15 下午7.28.35](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-15 下午7.28.35.png)

![屏幕快照 2020-12-15 下午7.28.43](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-15 下午7.28.43.png)

#### colorization

这个任务中输入的噪声比 MNIST 大一些。

训练的时候，最开始的迭代中，特征提取网络 $h$ (共有部分) 是不参与训练的，参与训练的只有多头结构。最后 $h$ 的参数也会参与训练。

因为是 colorization 任务，所以输入不要用原始的分辨率，这篇文章用的是一个较小的分辨率。在最终的推断阶段，会对生成的 colorization 进行 bilateral 上采样（这个不太知道是什么）

这个实验中，作者和 GAN、VAE 方法进行了比较。

数据操纵部分，主要是两个内容。一个是改变 $\mathbf{z}$ 的模长，观察结果的变化。一个是风格迁移，用其他图片获取隐变量，用于 target 图像。

![屏幕快照 2020-12-15 下午7.34.12](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-15 下午7.34.12.png)

![屏幕快照 2020-12-15 下午7.34.06](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-15 下午7.34.06.png)

#### 消融实验

作者设计的笑容实验说明噪声增广的作用很明显。初始化等虽然有用，但不是特别明显。

condition 的特征提取是非常重要的，因为这涉及到语义的部分，如果没有特征提取，效果就会很差。从这一点出发，我想到的是即使非 conditional 的问题，是不是仍然可以考虑某些语义的信息，这样可以实现更好的对隐变量的控制。