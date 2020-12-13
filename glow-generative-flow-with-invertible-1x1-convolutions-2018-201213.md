# Glow: Generative Flow with Invertible 1×1 Convolutions

[TOC]

### 信息

Authors: Diederik P. Kingma and Prafulla Dhariwal

OpenAI, Google AI

NIPS 2018

```latex
@inproceedings{kingma2018glow,
  title={Glow: Generative flow with invertible 1x1 convolutions},
  author={Kingma, Durk P and Dhariwal, Prafulla},
  booktitle={Advances in neural information processing systems},
  pages={10215--10224},
  year={2018}
}
```

引用 700+

[Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/glow-generative-flow-with-invertible-1x1-convolutions.pdf)



### 概括

这篇文章提出了 Glow 模型，是 normalizing flow 模型的一种。其主要的贡献是模型的设计。在以前的较为简单的模型上，增加了可逆的 1x1 卷积，act normalizatin 层，以及改进了 affine coupling layer 的初始化等等。理论上的创新并不多。

他们的实验验证了 1x1 卷积的有效性。在 flow 模型中，1x1卷积相当于对中间变量的一个重排，以此实现所有的参数都参与 affine coupling layer 的变换。另外，他们尝试了较大分辨率图像的生成。

关于数据操纵，作者的方法很简单。关于不同模型之间的比较，作者也进行了一些说明。

### Code

https://github.com/openai/glow.



### 模型

#### 背景

flow-based model: $\begin{equation}
\mathbf{z}=\mathbf{f}_{\theta}(\mathbf{x})=\mathbf{g}_{\theta}^{-1}(\mathbf{x})
\end{equation},$ $ \begin{equation}
\mathbf{f}=\mathbf{f}_{1} \circ \mathbf{f}_{2} \circ \cdots \circ \mathbf{f}_{K}
\end{equation}$.
$$
\begin{equation}
\mathbf{x} \stackrel{\mathbf{f}_{1}}{\longleftrightarrow} \mathbf{h}_{1} \stackrel{\mathbf{f}_{2}}{\longleftrightarrow} \mathbf{h}_{2} \cdots \stackrel{\mathbf{f}_{K}}{\longleftrightarrow} \mathbf{z}
\end{equation}
$$
一般把 $\mathbf{z}$ 定义为 $\mathbf{h}_K$，$\mathbf{x}$ 定义为 $\mathbf{h}_0$.
$$
\begin{equation}
\begin{aligned}
\log p_{\theta}(\mathbf{x}) &=\log p_{\theta}(\mathbf{z})+\log |\operatorname{det}(d \mathbf{z} / d \mathbf{x})| \\
&=\log p_{\theta}(\mathbf{z})+\sum_{i=1}^{K} \log \left|\operatorname{det}\left(d \mathbf{h}_{i} / d \mathbf{h}_{i-1}\right)\right|
\end{aligned}
\end{equation}
$$
为了简化行列式的计算，基本的想法是采用 Jacobian 矩阵是三角矩阵。这样行列式的计算就变成：
$$
\begin{equation}
\log \left|\operatorname{det}\left(d \mathbf{h}_{i} / d \mathbf{h}_{i-1}\right)\right|=\operatorname{sum}\left(\log \left|\operatorname{diag}\left(d \mathbf{h}_{i} / d \mathbf{h}_{i-1}\right)\right|\right)
\end{equation}
$$

#### 模块说明

主要的模块有三个，actnorm，1x1 卷积，affine coupling layer.

**actnorm**

scale 和 bias 的计算。他们是 channel-wise。初始化的值和最开始的数据有关，之后就当作一个可学习的参数计算。而不会考虑用数据来 normalization。

**1x1 卷积**

1x1 卷积的参数就是一个矩阵，为了可逆，矩阵是一个方阵，也就是说，通道数没有变化。如果直接计算矩阵的行列式，需要较多的计算，而且保持矩阵的可逆性也是必须的，所以作者考虑了 LU 分解。
$$
\begin{equation}
\mathbf{W}=\mathbf{P} \mathbf{L}(\mathbf{U}+\operatorname{diag}(\mathbf{s}))
\end{equation}
$$
行列式就变成 $\operatorname{sum}(\log |\mathbf{s}|)$。这个分解中的 $\mathbf{P}$ 在初始化的时候，先对一个随机的初始化的旋转矩阵计算出来，然后固定这个矩阵，而 $\mathbf{L}, \mathbf{U}$ 则是三角矩阵，$\mathbf{s}$ 是向量，这些参数是可学习的，因此能够保证矩阵可逆且可以发生改变。

**affine coupling layer**

这里用了特别的初始化方法，所欧的卷积层参数初始化都是 0，这样以来输出的 scale 和 bias 都是 0，也就是说最开始，这一层是一个恒同变换。

至于 affine coupling 的分量划分，作者仅仅考虑了 channel 维度的划分，而没有考虑 checkboard 的划分。

![屏幕快照 2020-12-13 下午9.11.39](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-13 下午9.11.39.png)

不同模块（层）的函数形式和行列式计算：

![屏幕快照 2020-12-13 下午9.11.49](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-13 下午9.11.49.png)

**permutation**

这个概念其实很重要，因为 affine coupling layer 有一些分量是不变的，为了让所有分量都参与计算，需要改变分量划分的顺序。从某种意义上说，就是重排。原来的方法（NVP）考虑的是比较简单的重排，这篇文章表示 1x1 卷积其实就是一种重排。作者在实验中比较了三种方式：1. 逆序重排，2. 随机重排，3. 1x1 卷积重排。

**整体结构**

保持了多尺度的结构，每个 level 会分离出一部分分量为隐变量，也有 squeeze 操作，这个和 NVP 是一样的。所以整体模型如上面的图 (b)

#### 和其他生成模型的比较

这篇文章把 GAN 这类方法单独列一类。其他的称为 likelihood-based methods。而后者有三类模型：自回归模型、VAE、基于 flow 的生成模型。

flow-based 模型的优势在于更准确的推断和似然估计，比自回归模型更快速。可以有有效隐变量操纵手段，用于下游任务。另外还提到可以减少内存。最后一点我个人存疑。



### 实验

下面的图说明了不同的重排方式的结果，1x1卷积是很有效的。

![屏幕快照 2020-12-13 下午9.12.02](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-13 下午9.12.02.png)

affine coupling layer 中的 scale 和 bias 的计算网络，使用的模型是三层卷积网络，ReLU 激活函数，512 channels. 三个卷积层分别是 3x3 卷积，1x1卷积，3x3 卷积。

网络大小：对于较小分辨率的数据集，作者用的是 K = 32, L = 3，对于较大的数据集，L = 6（也就是 level 更多了）。1x1 可逆卷积的参数量并不算太多，但是有效。

数据集大多数 32x32, 64x64, 96x96, 256x256 的分辨率（个人认为 L 的值和分辨率有直接关系，所以论文中 L = 3 存疑）

另外，作者表示，从生成结果上看，温度取比训练更小一点的值比较好，否则细节容易出现噪声。我认为这里是很值得思考的一个问题。

关于数据操纵，作者考虑的是独具 CelebA 人脸数据集，根据不同图像的 label （微笑等），不同 label 的图像的隐变量之间的差值的平均值，作为一个 manipulation 的方向，不过论文里讨论的不是太详细。

作者还考虑了不同 sample 之间的线性插值，观察了这个变化。

最后给一些图像：

![屏幕快照 2020-12-13 下午9.35.11](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-13 下午9.35.11.png)