# Nice: Non-linear independent components estimation

[TOC]

### 信息

Authors: Laurent Dinh, David Krueger, Yoshua Bengio

Université de Montréal

ICLR 2015

```latex
@article{dinh2014nice,
  title={Nice: Non-linear independent components estimation},
  author={Dinh, Laurent and Krueger, David and Bengio, Yoshua},
  journal={arXiv preprint arXiv:1410.8516},
  year={2014}
}
```

 [Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/nice-non-linear-independent-components-estimation.pdf)



### 概括

这篇文章构建了一个分布变换模型 Non-linear independent components estimiation (NICE)。这个模型顾名思义，就是将一个复杂的分布通过非线性变换到一个分量相互独立的分布，因此涉及到表示学习的问题。通过训练，可以得到一个生成模型，并且作者将其用于 inpainting 任务。

非线性变换的采用了多个函数复合的形式，要求这些函数可逆，且 Jacobian 矩阵易于计算。为此，作者采用了划分分量的方式，使得 Jacobian 矩阵的行列式值为 1. 而非线性变换函数则是一个全连接的神经网络。

作者考虑的数据集都是图像数据集，实验效果在现在看来很一般。



### 思路

#### 基本模型

作者认为一个好的表示学习，分布应该是简单的，所以考虑将复杂的分布变换到分量独立分布。设隐变量为 $h$，则有：$p_{H}(h)=\prod_{d} p_{H_{d}}\left(h_{d}\right)$. 如果存在可逆变换 $f$ 使得 $f(x) = h$，那么 $x$ 的分布就可以表示为：
$$
p_{X}(x)=p_{H}(f(x))\left|\operatorname{det} \frac{\partial f(x)}{\partial x}\right|
$$
inference 的时候，通过
$$
\log \left(p_{X}(x)\right)=\log \left(p_{H}(f(x))\right)+\log \left(\left|\operatorname{det}\left(\frac{\partial f(x)}{\partial x}\right)\right|\right)
$$
得到对数概率（这也就是损失函数的基本形式）。

sampling 的时候，先从隐变量采样，$h\sim p_H(h)$，然后计算 $x = f^{-1}(h)$

事实上这个模型和 VAE 很像，只不过 encoder 和 decoder 具有完美的可逆性。

#### 变换的选择

一般形式是：
$$
\begin{array}{l}
y_{I_{1}}=x_{I_{1}} \\
y_{I_{2}}=g\left(x_{I_{2}} ; m\left(x_{I_{1}}\right)\right)
\end{array}
$$
很容易验证这个形式的函数变换具有下三角的 Jacobian 矩阵。更一般的直接采用 $g(a, b) = a + b$.

将 $x$ 划分成 $(x_1, x_2)$，输出则是 $(y_1, y_2)$，有如下形式：
$$
\begin{array}{l}
y_{1}=x_{1} \\
y_{2}=x_{2}+m\left(x_{1}\right)
\end{array}
$$
逆变换很显然是：
$$
\begin{array}{l}
x_{1}=y_{1} \\
x_{2}=y_{2}-m\left(y_{1}\right)
\end{array}
$$
$m$ 可以任意复杂的函数，这篇文章采用的是 ReLU MLP

#### 整体结构

如果只有一层，就会有一部分输入是不变的，因此需要多个函数的复合。作者表示，至少需要三个，实验中使用了四个。由于变换形式 (4) 的 Jacobian 行列式为 1，因此属于 volume preserving

另外，作者还加了一个 scale 层，也就是每个分量乘上一个系数。最终的损失函数是：
$$
\log \left(p_{X}(x)\right)=\sum_{i=1}^{D}\left[\log \left(p_{H_{i}}\left(f_{i}(x)\right)\right)+\log \left(\left|S_{i i}\right|\right)\right]
$$


#### 隐变量分布

关于隐变量 $h$ 的分布选择，作者提到两种，一个是高斯分布，一个是 logistic 分布：

gaussian：
$$
\log \left(p_{H_{d}}\right)=-\frac{1}{2}\left(h_{d}^{2}+\log (2 \pi)\right)
$$
logistic：
$$
\log \left(p_{H_{d}}\right)=-\log \left(1+\exp \left(h_{d}\right)\right)-\log \left(1+\exp \left(-h_{d}\right)\right)
$$


### 实验

训练的时候对 $x$ 加了均匀噪声。

#### 表示学习

作者采用的变量划分的方式根据奇偶性划分变量，变换公式写成：
$$
\begin{aligned}
h_{I_{1}}^{(1)} &=x_{I_{1}} \\
h_{I_{2}}^{(1)} &=x_{I_{2}}+m^{(1)}\left(x_{I_{1}}\right) \\
h_{I_{2}}^{(2)} &=h_{I_{2}}^{(1)} \\
h_{I_{1}}^{(2)} &=h_{I_{1}}^{(1)}+m^{(2)}\left(x_{I_{2}}\right) \\
h_{I_{1}}^{(3)} &=h_{I_{1}}^{(2)} \\
h_{I_{2}}^{(3)} &=h_{I_{2}}^{(2)}+m^{(3)}\left(x_{I_{1}}\right) \\
h_{I_{2}}^{(4)} &=h_{I_{2}}^{(3)} \\
h_{I_{1}}^{(4)} &=h_{I_{1}}^{(3)}+m^{(4)}\left(x_{I_{2}}\right) \\
h &=\exp (s) \odot h^{(4)}
\end{aligned}
$$
其中，$m^{(i)}$ 是一个具有 5 个隐层 1000 个结点的神经网络。

但随机采样的效果其实挺一般的。

![屏幕快照 2020-12-11 下午3.03.01](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-11 下午3.03.01.png)

#### inpainting

假设观察到的图像为 $x_O$，未观察到的部分是 $x_H$，那么目标函数就是
$$
\max _{x_{H}} \log \left(p_{X}\left(\left(x_{O}, x_{H}\right)\right)\right)=\max _{x_{H}} \log \left(p_{H}\left(f\left(\left(x_{O}, x_{H}\right)\right)\right)\right)+\log \left(\left|\operatorname{det}\left(\frac{\partial f\left(\left(x_{O}, x_{H}\right)\right)}{\partial x}\right)\right|\right)
$$
通过下列的梯度迭代进行计算：
$$
\begin{aligned}
x_{H, i+1} &=x_{H, i}+\alpha_{i}\left(\frac{\partial \log \left(p_{X}\left(\left(x_{O}, x_{H, i}\right)\right)\right)}{\partial x_{H, i}}+\epsilon\right) \\
\epsilon & \sim \mathcal{N}(0, I)
\end{aligned}
$$
这里的 $\epsilon$ 是一个随机的噪声。