# Diffusion Models Beat GANs on Image Synthesis

[TOC]

## 信息

Authors: Prafulla Dhariwal and Alex Nichol

Year: arXiv 2021

Bibtex:

```latex
@article{dhariwal2021diffusion,
  title={Diffusion models beat gans on image synthesis},
  author={Dhariwal, Prafulla and Nichol, Alex},
  journal={arXiv preprint arXiv:2105.05233},
  year={2021}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/diffusion-models-beat-gans-on-image-synthesis.pdf)

code: https://github.com/openai/guided-diffusion



## 概括

这篇文章是对 DDPM 模型的进一步改进，通过这些改进，作者实现了扩散模型更高的生成质量，完全可以超过 BigGAN。这些改进主要是两个方面，一个是网络结构的改进，一个是增加了分类器的 guidance。这个分类器既可以用于类别条件的生成，也可以用于无类别条件的生成（只要这个生成数据是有类别的）。分类器的 guidance 是通过其梯度实现的，梯度的 scale 可以作为调节生成网络的 diversity 和 fidelity 的参数。在 ImageNet 数据集上，这个改进版本的 DDPM 实现非常好的 FID。关于快速 sampling，作者借鉴了此前的很多 DDPM 的改进。另外，作者的实验表示 upsampling stack 结构的生成和这篇文章提出的改进可以共同促进生成质量的提高。可以说这篇文章是一篇综合了之前很多改进的文章。



## 方法

首先作者说明了这篇文章利用的此前的相关改进工作。

1. 使用混合损失函数（这是作者自己提出来的）以及可学习的协方差。
2. 当快速采样的时候（少于 50 次），使用 DDIM 采样方法

总的来说，这篇文章的基本模型是之前的那篇 improved DDPM。

下面分为两个部分介绍改进。

### 网络结构改进

我们先说明作者考虑了哪些方面的改进，再说明实验的结果。

改进的方向：

1. 在网络大小不变的情况下，对深度和宽度进行调整
2. 增加自注意力的头结构数量
3. 在 32，16，8 分辨率都使用自注意力机制，不仅仅是 16 分辨率
4. 使用 BigGAN 的 residual block 作为上采样和下采样
5. 调整 residual connenctions 的比例，乘上 $\frac{1}{\sqrt{2}}$

在这个部分，作者用 ImageNet 128 作为训练，batch size 为256，分别训练 700k 和 1200k 迭代，inference 使用 250 个采样步。用 FID 作为比较的指标。

结果发现：

1. 除了 rescaling residual connections 的改进无效，其他都有正面的效果
2. 增加深度，减少宽度，虽然效果提升，但是训练的难度增加很多，所以不使用这个改进
3. 使用多头机制，每个头的channel数量少一些有效果。这种结构更接近 Transformer 的结构

关于标准化层，作者最后选择了 AdaGN，incorporates the timestep and class embedding into each residual block after a group normalization operation
$$
\operatorname{AdaGN}(h, y)=y_{s} \text { GroupNorm }(h)+y_{b}
$$
$y = [y_s, y_b]$ 是 a linear projection of the timestep and class embedding. h is the intermediate activations of the residual block following the ﬁrst convolution。这里的 timestep 和 class embedding 应该是相似的结构。但是具体怎么做需要看代码。

**最终网络的设定为**：

1. 每个分辨率有两个 residual block，宽度和分辨率有关
2. 自注意力机制用于 32，16，8 resolution
3. 多头，每个头 64 通道
4. BigGAN 的 residual block 结构
5. AdaGN 标准化层

### 分类器guidance

这个部分是作者的主要的创新。其实分类器辅助已经用于 GAN 的训练了。但是这篇文章将这种思路用于扩散模型。

分类器的基本想法是用于条件生成。Song Yang 等人的文章也提到了类似的做法。我们需要训练一个分类器 $p_{\phi}(y|x_t, t)$，使用这个分类器的梯度 $\nabla_{x_t} \log p_{\phi}(y|x_t, t)$，来帮助生成。（实际上就是在 inference 的采样过程中，增加这个梯度的计算，并辅助生成。

现在假设这个分类器我们已经训练好了

作者给出了两种方式，一种是不使用 DDIM 具有随机性的采样，一种是使用 DDIM 的不具有随机性的采样。

**第一种方式**

作者的推导思路是，我们需要计算出 $p(x_t|x_{t+1}, y)$ 是什么样的分布，这样就可以按照 DDPM 的逆扩散过程来进行采样。为了计算这个分布，作者通过它的分解形式和对数概率的估计，反推出这个条件概率应该是什么样的。

首先我们可以分解为：
$$
p_{\theta, \phi}\left(x_{t} \mid x_{t+1}, y\right)=Z p_{\theta}\left(x_{t} \mid x_{t+1}\right) p_{\phi}\left(y \mid x_{t}\right)
$$
这里的 Z 是归一化常数。注意，$x_{t+1}, y$ 是固定的。

我们先计算 $p_{\theta}$ 的对数形式
$$
\begin{aligned}
p_{\theta}\left(x_{t} \mid x_{t+1}\right) &=\mathcal{N}(\mu, \Sigma) \\
\log p_{\theta}\left(x_{t} \mid x_{t+1}\right) &=-\frac{1}{2}\left(x_{t}-\mu\right)^{T} \Sigma^{-1}\left(x_{t}-\mu\right)+C
\end{aligned}
$$
由于扩散模型的假设是时间 t 非常小，所以 $\Sigma$ 的对角线值都很小，因此实际上 $x_t$ 的范围应该是很小的，所以作者用泰勒展开来近似 $\log p_{\phi}$
$$
\begin{aligned}
\log p_{\phi}\left(y \mid x_{t}\right) &\left.\approx \log p_{\phi}\left(y \mid x_{t}\right)\right|_{x_{t}=\mu}+\left.\left(x_{t}-\mu\right) \nabla_{x_{t}} \log p_{\phi}\left(y \mid x_{t}\right)\right|_{x_{t}=\mu} \\
&=\left(x_{t}-\mu\right) g+C_{1}
\end{aligned}
$$
这里假设了 $g =\left.\nabla_{x_t} \log p_{\phi}\left(y \mid x_{t}\right)\right|_{x_{t}=\mu} $，$C_1$ 是由 $\mu$ 决定的，所以是常数。

将上述的两个式子合并，可以得到：
$$
\begin{aligned}
\log \left(p_{\theta}\left(x_{t} \mid x_{t+1}\right) p_{\phi}\left(y \mid x_{t}\right)\right) & \approx-\frac{1}{2}\left(x_{t}-\mu\right)^{T} \Sigma^{-1}\left(x_{t}-\mu\right)+\left(x_{t}-\mu\right) g+C_{2} \\
&=-\frac{1}{2}\left(x_{t}-\mu-\Sigma g\right)^{T} \Sigma^{-1}\left(x_{t}-\mu-\Sigma g\right)+\frac{1}{2} g^{T} \Sigma g+C_{2} \\
&=-\frac{1}{2}\left(x_{t}-\mu-\Sigma g\right)^{T} \Sigma^{-1}\left(x_{t}-\mu-\Sigma g\right)+C_{3} \\
&=\log p(z)+C_{4}, z \sim \mathcal{N}(\mu+\Sigma g, \Sigma)
\end{aligned}
$$
所以 $p_{\theta, \phi}\left(x_{t} \mid x_{t+1}, y\right)$ 也被看成是一个高斯分布，只是均值改变了。

于是我们只要计算 g，然后改动均值就可以了。这样就得到第一种采样。

![屏幕快照 2021-08-20 下午12.23.20](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-20 下午12.23.20-9435812.png)

**第二种方式**

DDIM 的核心是要知道 $\nabla_{x_{t}} \log \left(p_{\theta}\left(x_{t}\right) p_{\phi}\left(y \mid x_{t}\right)\right)$

利用 DDPM 和 score function 的关系，
$$
\begin{aligned}
\nabla_{x_{t}} \log \left(p_{\theta}\left(x_{t}\right) p_{\phi}\left(y \mid x_{t}\right)\right) &=\nabla_{x_{t}} \log p_{\theta}\left(x_{t}\right)+\nabla_{x_{t}} \log p_{\phi}\left(y \mid x_{t}\right) \\
&=-\frac{1}{\sqrt{1-\bar{\alpha}_{t}}} \epsilon_{\theta}\left(x_{t}\right)+\nabla_{x_{t}} \log p_{\phi}\left(y \mid x_{t}\right)
\end{aligned}
$$

$$
\hat{\epsilon}\left(x_{t}\right):=\epsilon_{\theta}\left(x_{t}\right)-\sqrt{1-\bar{\alpha}_{t}} \nabla_{x_{t}} \log p_{\phi}\left(y \mid x_{t}\right)
$$

用这个新的 $\hat{\epsilon_t}$ 替换到算法中就可以了。

![屏幕快照 2021-08-20 下午12.23.24](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-20 下午12.23.24.png)

在上述的两种方式中，都需要计算 $\nabla_{x_t} \log p_{\phi}\left(y \mid x_{t}\right)$，所以称为 gradient guidance。作者们使用的分类器就是一个 Unet 的下采样结构，使用了 attention pool 层。（对于我们的实验而言，分类器 guidance 是没有用的，但是也许可以借鉴这个思路到 MRI 重建中）

在实验中，作者发现如果梯度的 scale 系数是 1. （也就是保持原来的值）这会导致生成的结果并不完全符合类别，而使用大于1的scale 系数，则会使得生成质量提高。大于1的scale 系数相当于使用了一个更sharp 的分类器 $p(y|x)^s$。因此作者认为可以用 scale 来控制 fidelity 和 diversity。更大的 s，生成更高质量但是多样性弱的图像。

不过在刚才的说明中，分类器是用在无条件生成模型之上的。（也就是说生成模型本身是无条件训练的）。作者表示，这个方法同样可以用于有条件生成模型。如果无条件生成模型 + 高 scale 值的分类器梯度 guidance，效果和没有guidance的有条件生成模型差不多。但是guiding 有条件生成模型效果就更好了。（我个人认为 guidance 某种意义上就是为了生成有条件的图像）不过直接训练和这种方式仍然由差异，这也许说明如果我们用无条件生成模型来训练 MRI + 条件 guidance 的效果和直接用有条件生成模型，两者的结果是有差异的。而且即使是有条件的生成模型，还可以增加这个 guidance，这在 MRI 重建里应该是完全适用的。从实验结果上来看确实提高了很多。



## 实验

这篇文章的评价指标有：IS，FID，sFUD，Improved Precision 和 Recall。主要使用 FID。用于评估网络改进有效性的实验主要是 ImageNet 128.

实验的内容主要分为两个部分。一个部分是研究改进是否有效。一个部分是将这些改进用于生成其他数据集。

To evaluate our improved model architecture on unconditional image generation, we train separate diffusion models on three LSUN [71] classes: bedroom, horse, and cat. To evaluate classiﬁer guidance, we train conditional diffusion models on the ImageNet [52] dataset at 128×128, 256×256, and 512×512 resolution.

从不使用 guidance 的效果上看，无条件的生成已经超越了 styleGAN2。

另外作者还和使用 upsampling stack的方法进行了比较。仅仅使用 upsampling stack 的方法，还比不上 BigGAN-deep，但是这个方法和 guidance 结合之后变得更强了，比只使用 guidance 还强。

在最后的讨论部分，作者认为扩散模型的改进重点仍然是如何加快 sampling，另外分类器 guidance 的方法应该可以进一步拓展。即使是没有 label 的数据，也许也可以通过某种方式来实现 guidance。



## 参考文献

Charlie Nash, Jacob Menick, Sander Dieleman, and Peter W. Battaglia. Generating images with sparse representations. arXiv:2103.03841, 2021. 一个 SOTA 水平的生成模型。

Ali Razavi, Aaron van den Oord, and Oriol Vinyals. Generating diverse high-ﬁdelity images with VQ-VAE-2. arXiv:1906.00446, 2019. SOTA 水平的生成模型

Rewon Child. Very deep vaes generalize autoregressive models and can outperform them on images. arXiv:2011.10650, 2021.



## 更多细节

### 网络结构

在原来的 DDPM 模型基础上改进：

1. 每个分辨率有两个 residual block，宽度和分辨率有关
2. 自注意力机制用于 32，16，8 resolution
3. 多头，每个头 64 通道
4. BigGAN 的 residual block 结构
5. AdaGN 标准化层

网络参数参考训练参数设定。

### 损失函数

仍然是混合损失函数

### 数据预处理

无

### 训练参数设定

Adam or AdamW，$\beta_1 = 0.9, \beta_2 = 0.999$,16 bit 精度。

使用 EMA 方法。EMA 的比例是 0.9999

因为我们不会使用分类器网络，所以不考虑这个部分的训练。

![屏幕快照 2021-08-21 上午10.47.01](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-21 上午10.47.01.png)

![屏幕快照 2021-08-21 上午10.47.13](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-21 上午10.47.13.png)

### inference参数设定

在这篇文章，多了一个分类器 scale 的超参数。

如果是 250 次采样，选择的是 improved DDPM 的方法。如果是 25 次采样，选择的是 DDIM 方法。

### 实验结果

见论文。

### 代码

https://github.com/openai/guided-diffusion