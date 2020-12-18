# Analyzing inverse problems with invertible neural networks

[TOC]

### 信息

Authors: Lynton Ardizzone, Jakob Kruse, Sebastian Wirkert , Daniel Rahner, Eric W. Pellegrini, Ralf S. Klessen, Lena Maier-Hein, Carsten Rother, Ullrich Köthe

Visual Learning Lab Heidelberg, German Cancer Research Center, Zentrum für Astronomie der Universität Heidelberg

ICLR 2019

```latex
@article{ardizzone2018analyzing,
  title={Analyzing inverse problems with invertible neural networks},
  author={Ardizzone, Lynton and Kruse, Jakob and Wirkert, Sebastian and Rahner, Daniel and Pellegrini, Eric W and Klessen, Ralf S and Maier-Hein, Lena and Rother, Carsten and K{\"o}the, Ullrich},
  journal={arXiv preprint arXiv:1808.04730},
  year={2018}
}
```

引用：100+

[Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/analyzing-inverse-problems-with-invertible-neural-networks.pdf)



### 概括

这篇文章利用 flow-based 模型解决 inverse problem。不过这里的 inverse problem 不是线性逆问题，包含非线性的情形。由于是 flow-based 模型，其网络是可逆的。网络的输入是原始数据 x，输出是测量值 y 和隐变量 z。这个模型可以理解成建立 x 和 (y, z) 之间的变换，而 y, z 直接也满足一定的性质（通过损失函数）。在这个方法中，损失函数可能是一个关键点。

具体来说，损失函数包含三个部分。一个是 y 和真实的 y 之间的误差，因为可逆网络输出的一部分是 y，相当于这个网络也包含了对于 forward process 的逼近。第二个部分是关于 y, z 的独立性。第三个部分是 x 自身的似然函数。具体参见笔记。

隐变量 z 可以理解成 x 到 y 的测量过程中丢失的信息的一种表示。这种信息是 ill-posed，所以用隐变量 z 的分布来表示这种不确定性。当模型训练好之后，通过 (y, z) 就可以反推出 x。

这篇文章的实验有模拟数据，也有真实的数据，但是数据的维度都不高，和图像这样的数据相比，有较大的差异，因此我个人认为这篇文章的启发性不高。



### 方法

设信号 $\mathbf{x}$，测量值为 $\mathbf{y}$，满足 forward process $\mathbf{y} = s(\mathbf{x})$。目标是估计 $p(\mathbf{x}|\mathbf{y})$。数据集是 $(\mathbf{x}, \mathbf{y})$ 这样的数据对。设隐变量 $\mathbf{z}$ 是正态分布，神经网络 $g$ 将 $\mathbf{y}, \mathbf{z}$ 映射到 $\mathbf{x}$：
$$
\begin{equation}
\mathbf{x}=g(\mathbf{y}, \mathbf{z} ; \theta) \quad \text { with } \quad \mathbf{z} \sim p(\mathbf{z})=\mathcal{N}\left(\mathbf{z} ; 0, I_{K}\right)
\end{equation}
$$
神经网络 $g$ 的逆（也就是这个网络本身）是 $f$，则有：
$$
\begin{equation}
[\mathbf{y}, \mathbf{z}]=f(\mathbf{x} ; \theta)=\left[f_{\mathbf{y}}(\mathbf{x} ; \theta), f_{\mathbf{z}}(\mathbf{x} ; \theta)\right]=g^{-1}(\mathbf{x} ; \theta) \quad \text { with } \quad f_{\mathbf{y}}(\mathbf{x} ; \theta) \approx s(\mathbf{x})
\end{equation}
$$
在这里，$f_{\mathbf{y}}$ 相当于逼近了 $s$。

利用 flow-based 模型的性质，可以得到条件概率 $q(\mathbf{x}|\mathbf{y})$ 是：
$$
\begin{equation}
q(\mathbf{x}=g(\mathbf{y}, \mathbf{z} ; \theta) \mid \mathbf{y})=p(\mathbf{z})\left|J_{\mathbf{x}}\right|^{-1}, \quad J_{\mathbf{x}}=\operatorname{det}\left(\left.\frac{\partial g(\mathbf{y}, \mathbf{z} ; \theta)}{\partial[\mathbf{y}, \mathbf{z}]}\right|_{\mathbf{y}, f_{\mathbf{z}}(\mathbf{x})}\right)
\end{equation}
$$
Jacobian 矩阵是很好计算的（因为是 flow-based model）

具体的网络结构和 Real NVP 很像，都是将 input 拆成两个部分，然后用神经网络表示 scale 和 bias。在这篇文章里，作者把两个 affine coupling layer 写成一个形式：
$$
\begin{equation}
\mathbf{v}_{1}=\mathbf{u}_{1} \odot \exp \left(s_{2}\left(\mathbf{u}_{2}\right)\right)+t_{2}\left(\mathbf{u}_{2}\right), \quad \mathbf{v}_{2}=\mathbf{u}_{2} \odot \exp \left(s_{1}\left(\mathbf{v}_{1}\right)\right)+t_{1}\left(\mathbf{v}_{1}\right)
\end{equation}
$$
对应的，逆运算也可以写出来。作者采用的计算 scale 和 bias 的神经网络是全连接网络。

两个技巧：

1. 如果信号的维度比较小，作者发现将信号的维度拓宽，类似 padding 补 0 的方式增加维度，这样可以增加网络内部节点的数量，以提升网络的表达能力。
2. 在两个 affine coupling layer 之间，作者使用了重排。重排的方式是固定的，在训练之初就确定好。其效果类似于 Glow 模型中的 1x1 卷积。



### 训练

这里单独把训练提出来说明。

作者把训练称之为双向训练。

损失函数中的第一个部分是 $\mathcal{L}_\mathbf{y}$，这是一个监督损失函数，和 $\mathbf{y}_i，f_\mathbf{y}(\mathbf{x}_i)$ 有关，一般是一个回归的 2范数 loss 或者分类的 cross-entropy loss。这和具体的 inverse problem 有关。

第二个 loss 惩罚的是联合分布和边缘分布之间的关系。即 $\begin{equation}
\mathcal{L}_{\mathbf{z}}(q(\mathbf{y}, \mathbf{z}), p(\mathbf{y}) p(\mathbf{z}))
\end{equation}$ 。虽然这个损失函数和 $\mathbf{y}, \mathbf{z}$ 都有关系，但是作者仅仅考虑对  $\mathbf{z}$ 的影响，而不改变 $\mathbf{y}$（因为 $\mathbf{y}$ 应该是和 forward process 有关，不应该修改，这个 loss 是通过对 $\mathbf{z}$ 的变动使得两个分布是接近的。这个损失函数有两个目的：1. 让 $\mathbf{z}$ 的生成更接近于正态分布，2. 让 $\mathbf{z}$ 关于 $\mathbf{y}$ 是独立的。具体的损失函数是 Maximu Mean Discrepency (但是我不懂是什么损失函数，只知道这个和分布之间的比较是相关的，也许类似于 KL 散度)

使用 MMD 损失函数的好处是只需要 sample 的信息。

作者给了一个定理，说明这两个损失函数的有效性。

![屏幕快照 2020-12-18 下午3.49.36](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-18 下午3.49.36-8277816.png)

第三个损失函数是和 $\mathbf{x}$ 有关，同样也是用 MMD，作者希望模型估计的概率 $\begin{equation}
q(\mathbf{x})=p\left(\mathbf{y}=f_{\mathbf{y}}(\mathbf{x})\right) p\left(\mathbf{z}=f_{\mathbf{z}}(\mathbf{x})\right) /\left|J_{\mathbf{x}}\right|
\end{equation}$ 和真实的 $p(\mathbf{x})$ 是接近的。因此有 $\mathcal{L}_x(p(\mathbf{x}), q(\mathbf{x}))$作者证明了当 $\mathcal{L}_z, \mathcal{L}_y$ 收敛到 0 的时候 $\mathcal{L}_x$ 也收敛到 0.

MMD 方法感觉略复杂，这里就不仔细讨论了。它涉及到 kernel 的选择。



实验部分略