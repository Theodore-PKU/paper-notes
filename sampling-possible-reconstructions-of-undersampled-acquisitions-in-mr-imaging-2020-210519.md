# Sampling possible reconstructions of undersampled acquisitions in MR imaging

[TOC]

## 信息

Authors: Kerem C. Tezcan, Christian F. Baumgartner and Ender Konukoglu

Year: 2020 arXiv

Bibtex:

```latex
@article{tezcan2020sampling,
  title={Sampling possible reconstructions of undersampled acquisitions in MR imaging},
  author={Tezcan, Kerem C and Baumgartner, Christian F and Konukoglu, Ender},
  journal={arXiv preprint arXiv:2010.00042},
  year={2020}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/sampling-possible-reconstructions-of-undersampled-acquisitions-in-mr-imaging.pdf)



## 概括

这篇文章研究的是 MRI 重建中的不确定性问题。核心想法是通过生成多个重建结果，再利用这些重建结果进行统计估计，比如 mean，std 等。生成多个重建结果的基本方法是：首先训练一个 VAE，生成图像；第二步则是利用 MALA 的采样方法，采样出隐变量 z ～ p(z|y)，然后通过 decoder 获取 p(x|z) 的均值，用均值当作是生成的结果。论文主要的亮点应该是 MALA 采样方法。这在方法的小节中会详细说明。

这篇文章所实验的实验数据集规模很大，比较实验主要是一个使用 WGAN 的方法。对于不确定性估计的比较，这篇文章也值得注意。相关内容会在实验的小节中详细说明。



## 方法

在 introduction 中，作者首先对重建问题中的 deterministic 和不确定性进行了分析。作者的分析我认为还是很准确的，因此这里做一个简单地说明。关于确定性方法，这种重建方法的目的是获得一个最好的图像，这个最好，往往是在某种 prior 意义下。但是，正因为仅仅是一个图像，忽略了 MRI 重建（逆问题）中的不确定性的本质，因此也无法分析误差，而且这个 best 图像也不一定就是 true image。在 MRI 重建中，问题本身就包含 inherent ambiguity. 此外，作者还区分了另外两种不确定性，一种是模型本身的不确定性，即 epistemic or model uncertainty，这种不确定性来自网络本身的参数不确定。还有一种不确定性是来自训练数据中的噪声或者其他的误差，这被称为 aleatoric uncertainty。

作者表示，如果仅仅是生成 pixel 级别的 variance，并不够充分，特别是这种 variance 没有考虑像素之间的相关性，和生成多种重建结果相比是较差的选择。

作者引用了两篇相关的工作，一篇是 Adler 的关于 CT 重建的文章，主要方法是 cWGAN，可以生成多种不同的结果，但作者认为没有考虑到 forward model. 另一篇是 Pedemonte 的 PET 重建的方法，作者认为他们的鲜艳分布是不够准确的（用了 non-informative prior）。这两篇文章以后可以看看作为文献综述的一部分，但是不会用来比较模型。这篇文章倒是可以用来比较模型（但是方法太复杂了，而且没有给代码，打算放弃这个想法了，但是我们自己似乎可以考虑用这个采样方法？直接用一个 flow 模型估计 $p(z|y)$）。

作者的基本想法如概括中所述，作者认为图像在隐变量空间是低维的，所以更容易进行 sample，然后利用 decoder 的隐变量和图像的关系来获取重建结果。因此这个解决方案分为两个部分，首先是 VAE 表示的图像分布，另一个则是隐变量的采样方法。按照论文的顺序，我们先讨论后者，然后再叙述 VAE 的部分。需要说明的是，这两个部分基本上是独立的，也就是说可以用其他的采样方法，也可以用其他的 VAE 模型。

先介绍一些记号。作者在这篇文章中考虑的是多线圈的 MRI 重建。假设有 c 个线圈，模型记为：
$$
y = Ex + \eta
$$
$x \in C^{N}, y \in C^{Mc}$，M < N，N 是图像的维度大小，M 则是每个线圈采样到的 k-space 数量。E 表示多线圈测量矩阵，这个矩阵在作者的设定下非常复杂，我们在这里不详细说明，参见2.5小节。在复现这篇文章的时候，我也不打算考虑这么复杂的矩阵 E，即使是多线圈的情形，我们也会尽量简单化。

噪声 $\eta$ 的分布是 $N(0, \Sigma_{ns})$，因此数据似然项 $p(y|x) = N(y; Ex, \sigma_{ns})$.

### 隐变量采样

假设 VAE 的 encoder 表示分布 $q(z|x) = N(\mu_z(x), \Sigma_z(x))$，decoder 表示分布 $p(x|z) = N(\mu_x(z), \Sigma_x)$。$z \in R^D$，维度比 N 小。隐变量的分布设为 $p(z) = N(\mu_{pr}, \Sigma_{pr})$. 隐变量的分布和训练时的 prior 是不同的，会重新估计，这个在 VAE 的训练部分说明，这里先默认已知。

这篇文章假设 $\Sigma_z$ 是一个对角矩阵，但对角线值不同。而 $\Sigma_x$ 是一个对角线值都相同的对角矩阵。换言之，VAE 的方差的假设很简单。

在 VAE 训练完毕之后，按照作者的想法，我们应该实现 p(z|y) 的采样。MALA 方法的全称是 Metropolis adjusted Langevin algorithm，这是一种统计上马尔可夫链蒙特卡洛方法，利用的是随机游走和 Metropolis-hastings 算法来决定是否接受游走。对于要采样的分布 p(z|y)，随机游走的式子为：
$$
\hat{z}^{t+1}=z^{t}+\left.\tau \nabla_{z} \log p(z \mid y)\right|_{z=z^{t}}+\sqrt{2 \tau} \zeta, \quad \zeta \sim N(0,1)
$$
$ \zeta$ 是一个随机变量的随机采样，$\tau$ 则是游走的步长。$\hat{z}_{t+1}$ 之所以带了个帽子是因为还没有决定是否要接受这个游走。接受概率的对数满足
$$
\alpha = \min \left\{0, \log \left[\frac{p\left(\hat{z}^{t+1} \mid y\right) q\left(z^{t} \mid \hat{z}^{t+1}\right)}{p\left(z^{t}\right) q\left(\hat{z}^{t+1} \mid z^{t}\right)}\right]\right\}
$$
如果接受，那么 $z_{t+1} = \hat{z}_{t+1}$，否则 $z_{t+1} = z_{t}$. 

在整个随机游走的过程中，(2) 中的梯度，和 (3) 中的概率计算。这里很奇怪的是，作者都不用考虑这些分布的归一化常数项（特别是在 (3) 中，按理这个是有影响，但是作者论文里反而说这个不需要）

在 (3) 中，我们要计算四个概率值，其中 $p(z_t)$ 是很容易计算的，$q(z_t|\hat{z}_{t+1}), q(\hat{z}_{t+1}|z_t)$ 的计算满足
$$
q(z^{\prime}|z) \propto \exp \left(-\frac{1}{4 \tau}\left\|z^{\prime}-z-\tau \nabla \log p(z \mid y)\right\|_{2}^{2}\right)
$$
这个计算也不考虑归一化就显得很奇怪。(4) 的计算涉及到 $p(z|y)$ 的梯度（如果这个已知了，就很好算了），这个也和 (2) 的计算相关，另外 (3) 的最后一项 $p\left(\hat{z}^{t+1} \mid y\right)$ 也涉及到 $p(z|y)$，实际计算 $p(z|y)$ 利用的是 $p(z|y) \propto p(y|z)p(z)$。我们只能认为作者不计算 $p(y)$ 了。因此接下来的核心就是计算 $\log p(z|y)$ 及其梯度。

利用 $p(z|y) \propto p(y|z)p(z)$，$p(z)$ 已知，而且 $\log p(z)$ 的相关计算都很简单，所以我们只要考虑计算 $p(y|z)$. 利用
$$
p(y \mid z)=\int p(y, x \mid z) d x=\int p(y \mid x) p(x \mid z) d x
$$
这里其实用了一个假设 $p(y|x, z) = p(y|x)$. 由于 (5) 中的每一项都是高斯分布，有解析形式，所以可以计算出 $p(y|z)$ 的解析解。
$$
\log p(y \mid z)=\mu_{x}^{H} \Sigma_{x}^{-1}\left(\Sigma_{x}^{-1}+E^{H} \Sigma_{n s}^{-1} E\right)^{-1} \Sigma_{x}^{-1} \mu_{x} \\ +2 \operatorname{Re}\left\{y^{H} \Sigma_{n s}^{-1} E\left(\Sigma_{x}^{-1}+E^{H} \Sigma_{n s}^{-1} E\right)^{-1} \Sigma_{x}^{-1} \mu_{x}\right\} \\- \frac{1}{2} \mu_{x}^{H} \Sigma_{x}^{-1} \mu_{x}+C
$$
这个式子的推导在附录中。我没有检查其正确性。$^H$ b表示共轭转置。在求梯度的时候，C 没有用，可以忽略。这个式子里，最难计算的是 $\left(\Sigma_{x}^{-1}+E^{H} \Sigma_{n s}^{-1} E\right)^{-1}$. 作者的想法是用一个迭代方法来计算 $\left(\Sigma_{x}^{-1}+E^{H} \Sigma_{n s}^{-1} E\right)^{-1} \Sigma_{x}^{-1} \mu_{x} $，即
$$
\gamma^{*}=\min _{\gamma}\left\|\left(\Sigma_{x}^{-1}+E^{H} \Sigma_{n s}^{-1} E\right) \gamma-\Sigma_{x}^{-1} \mu_{x}\right\|_{2}^{2}
$$
这个式子可以用 CG 算法得到。假设这个结果已知了，那么剩下的计算就是
$$
\log p(y \mid z)=\mu_{x}^{H} \Sigma_{x}^{-1} \gamma^{*}+2 \operatorname{Re}\left\{y^{H} \Sigma_{n s}^{-1} E \gamma^{*}\right\}-\frac{1}{2} \mu_{x}^{H} \Sigma_{x}^{-1} \mu_{x}
$$
当 CG 算法的迭代步数 $N_\gamma$ 固定的时候，$\log p(y|z)$ 的计算过程就是关于 $z$ 的一个函数，所以可以通过自动微分直接得到梯度。那么利用 $\nabla_{z} \log p(z \mid y)=\nabla_{z} \log p(y \mid z)+\nabla_{z} \log p(z)$ 就可以计算出我们要的梯度值。然后这个 MALA 算法就可以实现了。

在上述的计算中，需要设定的参数有：

1. 步长 $\tau$
2. decoder 的方差 $\Sigma_x$

需要估计的参数：

1. E
2. 噪声的方差 $\Sigma_{ns}$
3. 隐变量分布 $N(\mu_{pr}, \Sigma_{pr})$

最后一点是 MALA 采样的初值。为了加快采样，作者设定的初值是用 MAP 方法计算得到的 z 作为 $z_0$，不过在这个 VAE 方法里怎么算 MAP 似乎没有详细说明。

### VAE 的训练

作者考虑的 VAE 的隐变量是2D 的隐变量，有 D 个通道。训练的时候，使用标准高斯分布。训练完之后，需要估计实际的 $p(z) = N(\mu_{pr}, \Sigma_{pr})$。从训练数据 $x$ 中 采样 $z_i \sim q(z|x)$，得到非常多的 z 之后，估计均值和协方差矩阵。但是估计协方差矩阵的方法非常复杂。

#### VAE 的结构

卷积都是 padding 3x3 卷积，1x1 stride，Relu 激活函数。

encoder 一开始是4个卷积层，通道数为 32，64，64，64. 然后是一个 kernel size 为 14x14  stride 为 19x19 的卷积层，通道数变成 60，计算隐变量的均值。另外一个卷积层是从第三个卷积层那里开始计算，得到 log 标准差，也是 14x14 的 kernel size，19x19 的 stride。注意均值是从第四个卷积层那里开始计算的。z 的 sample 还是重参数方法。除了计算均值和方差的卷积层，其他卷积并没有下采样，因此保持了原来的分辨率大小。

decoder 的第一个卷积层没有用 Relu，输出通道数是 64x19 = 1216，此时保持了原来 z 的分辨率大小 18x22。然后这个卷积层的输出 tensor 从 batch x 18 x 22 x 1216 先转置成 batch x 18 x 1216 x 22，然后 reshape 到 batch x 252 x 64 x 22，然后在转置为 batch 252 x 22 x 64。之后接下一个没有 relu 的卷积层，通道数变成 1216，然后再 reshape 成 batch x 252 x 308 x 64，此时变成了原始的分辨率，这个时候tensor会传入 relu 激活函数。最后用 6 个卷积层，每个卷积层的输出通道数都是 60。然后是最终的输出层，用单通道输出结果（均值），方差在这篇文章已经被固定了。这里输出是单通道原因是作者把相位的部分合到 E 中去了，和 mag 图像分开处理。我们不一定参考这种做法。



### 其他方法

关于 cWGAN 的方法，作者提的不多。

利用 VAE，作者还提出了一些其他的简单获取不同重建结果的方法。第一个是利用 MAP image，通过 encoder 得到对应的 z 的分布，直接用这个 q(z|x) 分布来采样 z，然后由 decoder 计算出对应的 x。这样也可以采样出很多不同的 x。这个方法被称为 local sampling。

第二种 VAE 方法是不使用 KL 散度项的 VAE（也就是 AE）模型，但是 decoder 的输出是均值和pixel 方差。输入是 cWGAN 一样的 zero-filled 重建结果，当做是一般的监督学习任务。

## 实验

实验数据是 HCP dataset（用于训练和测试）和另外一个 in-house 数据（用于测试），total 202800 slices，size 是 252x308，为了让图像大小一致，作者对图像进行了相应的裁剪。因为是多线圈，所以用 ESPIRiT 估计了敏感矩阵。采样 mask 采用的是笛卡尔采样。

D. V. Essen, K. Ugurbil, E. Auerbach, D. Barch, T. Behrens, R. Bucholz, A. Chang, L. Chen, M. Corbetta, S. Curtiss, S. D. Penna, D. Feinberg, M. Glasser, N. Harel, A. Heath, L. LarsonPrior, D. Marcus, G. Michalareas, S. Moeller, R. Oostenveld, S. Petersen, F. Prior, B. Schlaggar, S. Smith, A. Snyder, J. Xu, and E. Yacoub, “The human connectome project: A data acquisition perspective,” NeuroImage, vol. 62, no. 4, pp. 2222 – 2231, 2012, connectivity.

U. Martin, L. Peng, M. M. J., V. Patrick, E. Michael, P. J. M., V. S. S., and L. Michael, “Espiritan eigenvalue approach to autocalibrating parallel mri: Where sense meets grappa,” Magnetic Resonance in Medicine, vol. 71, no. 3, pp. 990–1001, 2014.

比较的方法有三个，如上一节中的其他方法所叙述。

参数设置：$\tau = 4\times 10^{-4}$，目的是让接受概率大概是 0.3～0.5。$\Sigma_x $ 的值设为 0.2。

$\Sigma_{ns}$ 是用不同线圈的 k-space 估计的，这个我不太清楚具体怎么算，感觉作者没写清楚。

CG 迭代算法的迭代次数是 25

实验结果：

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-05-20 下午7.42.39.png" alt="屏幕快照 2021-05-20 下午7.42.39" style="zoom:50%;" />

作者分析不同方法的差异的时候，看的是 std 的模糊程度（这有点好笑）. 总的来说这篇文章的评估手段还是太 naive 了。

std 和 error 的关系，以及用直方图等形式来展示结果。

