# SRFlow: Learning the Super-Resolution Space with Normalizing Flow

[TOC]

### 信息

authors: Andreas Lugmayr, Martin Danelljan, Luc Van Gool, and Radu Timofte

Computer Vision Laboratory, ETH Zurich

ECCV 2020

```latex
@inproceedings{lugmayr2020srflow,
  title={Srflow: Learning the super-resolution space with normalizing flow},
  author={Lugmayr, Andreas and Danelljan, Martin and Van Gool, Luc and Timofte, Radu},
  booktitle={European Conference on Computer Vision},
  pages={715--732},
  year={2020},
  organization={Springer}
}
```



### 概括

在超分辨任务中，使用 normalizing flow 模型实现具有条件分布性质的超分辨结果。由于应用 normalizing flow 模型，损失函数只有一个。作者将该模型和机遇 GAN 的超分辨模型进行了比较。另外，利用 normalization 技巧将该模型应用于其他的一些视觉底层任务（图像操纵、去噪）中。



### 代码

[code](https://git.io/SRFlow)



### 思路

在超分辨任务中，最开始应用深度学习的方法是用 L1 或 L2 loss 进行重建，但是这种做法容易产生较为模糊的预测。L1 或 L2 loss 倾向于产生平均的效果。

之后使用 GAN 的对抗训练和 perceptual loss 增加预测的细节，但是仍然是一个确定性的映射。

超分辨任务本质上是一个 ill-posed 的问题，对于一个 LR 图像，HR 图像应该是有多个解，所以预测应当是一个分布，而不是确定性的结果。因此这篇文章考虑使用 normalizing model 来实现超分辨任务上的分布预测。

和 GAN 方法相比，避免了 GAN 训练的 mode-collapse，不需要调多个损失函数的参数，由于网络结构的可逆性，可以设计出相关的图像操纵的技术。

文章贡献：

1. 第一个 conditional normalizing flow 结构、并且在超分辨任务上取得 SOTA 效果的方法。
2. 发展出一个有效的图像操纵技术。
3. 该模型甚至可以用于图像去噪和恢复任务。
4. 实验结果显示比 SOTA GAN 方法要更好。

和 [49] 的区别：

1. provides favorable or superior results compared to state-of-the-art GAN-based methods.
2. develop powerful ﬂow-based image manipulation techniques.
3. introduce new training and architectural considerations.
4. applying SRFlow to image restoration tasks, unseen during training.

整体而言，只要获得了 $\boldsymbol{z}$，就可以结合 $\boldsymbol{x}$ 得到最终的图像。利用这个性质，可以实现一些图像操纵的技巧。



### 方法

#### 模型推导

这里采用和论文稍有不同的记号。

在给定 LR image $\boldsymbol{x}$ 的情况下，真实的 HR image $\boldsymbol{y}$ 满足一定的分布，假设有一个模型 $f_{\boldsymbol{\theta}}$ 将 $\boldsymbol{x}, \boldsymbol{y}$ 映射到一个隐变量 $\boldsymbol{z}$. 因为 $\boldsymbol{x}$ 是给定的，所以记 $\boldsymbol{z} = f_{\boldsymbol{\theta};\boldsymbol{x}}(\boldsymbol{y})$. 而且隐变量 $\boldsymbol{z}$ 和隐变量 $\boldsymbol{y}$ 之间的映射是可逆的，即 $\boldsymbol{y} = f_{\boldsymbol{\theta};\boldsymbol{x}}^{-1}(\boldsymbol{z})$. 假设隐变量也满足一定的分布（比如高斯分布），那么对于 $\boldsymbol{y}$ 的抽样可以由 $\boldsymbol{z}$ 的抽样和 $f_{\boldsymbol{\theta};\boldsymbol{x}}^{-1}(\boldsymbol{z})$ 得到。normalizing flow 的核心就是 $\boldsymbol{y}$ 的概率分布和 $\boldsymbol{z}$ 的概率分布满足（给定 $\boldsymbol{x}$）：
$$
p_{\boldsymbol{y}}(\boldsymbol{y}) = p_{\boldsymbol{z}}\left(f_{\boldsymbol{\theta} ; \mathbf{x}}(\boldsymbol{y})\right)\left|\operatorname{det} \frac{\partial f_{\boldsymbol{\theta}; \boldsymbol{x}}}{\partial \boldsymbol{y}}(\boldsymbol{y})\right|
$$
(1) 式成立的原因是概率密度的变量替换公式。如果随机变量 $u, v$ 满足 $g(u) = v, h(v) = u$，那么 $p_u(u) = p_v(g(u))|h'(u)|$. 对于多元随机变量，$|\cdot|$ 变成了行列式的绝对值。

(1) 式成立也是显然的，两边对 $\boldsymbol{y}$ 积分：
$$
\int p_{\boldsymbol{y}}(\boldsymbol{y}) d \boldsymbol{y} = \int p_{\boldsymbol{z}}\left(f_{\boldsymbol{\theta} ; \boldsymbol{x}}(\boldsymbol{y})\right)\left|\operatorname{det} \frac{\partial f_{\boldsymbol{\theta}; \boldsymbol{x}}}{\partial \boldsymbol{y}}(\boldsymbol{y})\right| d \boldsymbol{y} = \int p_{\boldsymbol{z}}(\boldsymbol{z}) d \boldsymbol{z} = 1
$$
假如有样本对 $(\boldsymbol{x}, \boldsymbol{y})$，那么 $\boldsymbol{y}$ 的概率应该是比较高的（既然样本对存在），所以 $f_{\boldsymbol{\theta}}$ 应该尽可能地使 (1) 式的结果尽可能大。对应极小化负的对数似然概率（negative log-likelihood, NLL）
$$
\mathcal{L}(\boldsymbol{\theta} ; \boldsymbol{x}, \boldsymbol{y})=-\log p_{\boldsymbol{y} \mid \boldsymbol{x}}(\boldsymbol{y} \mid \boldsymbol{x}, \boldsymbol{\theta})=-\log p_{\boldsymbol{z}}\left(f_{\boldsymbol{\theta}; \boldsymbol{x}}(\boldsymbol{y} )\right)-\log \left| \operatorname{det} \frac{\partial f_{\boldsymbol{\theta} ; \boldsymbol{x}}}{\partial \boldsymbol{y}}(\boldsymbol{y})\right|
$$
当 $f_{\theta}$ 训练好以后，有了 $\boldsymbol{x}$，就可以通过对 $\boldsymbol{z} \sim p_{\boldsymbol{z}}$ 的采样得到 $\boldsymbol{y}$.

> 虽然 $(\boldsymbol{x}, \boldsymbol{y})$ 的 pair 只有一个，但是只要不同的 pair 之间，存在局部上的相似性，那么就足以增加 $\boldsymbol{y}$ 的丰富度。

由于要计算行列式，就必须对网络有一定的要求。这里的假设是网络可以用多个可逆的层的复合来表示。设 $f_{\boldsymbol{\theta}} = f_{\boldsymbol{\theta}}^{N-1} \circ f_{\boldsymbol{\theta}}^{N-2} \circ \cdots \circ f_{\boldsymbol{\theta}}^{0}$，$\boldsymbol{h}^{n+1} = f_{\boldsymbol{\theta}}^{n}(\boldsymbol{h}^n;g_{\boldsymbol{\theta}}(\boldsymbol{x})), \boldsymbol{h}^0 = \boldsymbol{y}, \boldsymbol{h}^{N} = \boldsymbol{z}$. 那么 (3) 式可以表示成：
$$
\mathcal{L}(\boldsymbol{\theta} ; \boldsymbol{x}, \boldsymbol{y})=-\log p_{\boldsymbol{z}}(\boldsymbol{z})-\sum_{n=0}^{N-1} \log \left|\operatorname{det} \frac{\partial f_{\boldsymbol{\theta}}^{n}}{\partial \boldsymbol{h}^{n}}\left(\boldsymbol{h}^{n} ; g_{\boldsymbol{\theta}}(\boldsymbol{x})\right)\right|
$$
这里并不直接使用 $\boldsymbol{x}$ 而是对 $\boldsymbol{x}$ 做了特征提取（$g$, encode），具体的做法后续说明。

#### 网络结构设计

![屏幕快照 2020-12-07 下午8.22.12](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-07 下午8.22.12.png)

整体的网络结构如图所示。首先需要说明的是，$\boldsymbol{z}$ 似乎是多个变量，对应多个 level，每个 level 内部可以看成是多个 $f_{\boldsymbol{\theta}}^n$.

**Squeeze**

目的是对分辨率进行减半。实际的做法是集那个空间上的 2x2 相邻的特征叠起来。

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-07 下午8.32.07.png" alt="屏幕快照 2020-12-07 下午8.32.07" style="zoom:50%;" />

可以参考 [21] 

**Actnorm**

不同于 BN 层，这个 normalization 层只包含一个 scaling 和 bias 的参数，作用对象是特征图的每一个维度。（感觉有一点类似 instance normalization）

具体的需要参考 [21] 

**1x1 Convoluiton**

通常的卷积层是无法进行你运算的，而且也很难计算行列式的值。但是 1x1 卷积可以实现这一点。

如果要实现可逆的计算，对 1x1 卷积的参数应该也有一些要求，否则逆矩阵不一定存在。

具体的需要参考 [21] 

**Affine injector**

这一层的公式可以表达为：
$$
\mathbf{h}^{n+1}=\exp \left(f_{\boldsymbol{\theta}, \mathrm{s}}^{n}(\mathbf{u})\right) \cdot \mathbf{h}^{n}+f_{\boldsymbol{\theta}, \mathrm{b}}(\mathbf{u})
$$
这里的 $f_{\boldsymbol{\theta}, \mathbf{s}}^{n}, f_{\boldsymbol{\theta}, \mathbf{b}}^{n}$ 分别起到 scale 和 bias 的作用，它们可以是任意的网络结构，对它们而言不需要考虑可逆的问题。实际的做法是一个 conv-ReLU 模块降维至 64，然后接另一个 conv-ReLU 模块，输出仍然是 64 通道，在分别用两个不同的卷积层（64 通道）得到 scale 和 bias。$\mathbf{u}$ 则是 $\boldsymbol{x}$ 的特征提取 $g_{\boldsymbol{\theta}}(\boldsymbol{x})$。这里的 $\mathbf{h}$ 应该是前一层的输出

很显然，(5) 式是可逆的，而且行列式的对数也是可计算的 $\sum_{i j k} f_{\boldsymbol{\theta}, \mathbf{s}}^{n}\left(\mathbf{h}_{A}^{n} ; \mathbf{u}\right)_{i j k}$. 

**Conditional Affine Coupling**

这一层的公式可以表达为：
$$
\mathbf{h}_{A}^{n+1}=\mathbf{h}_{A}^{n}, \quad \mathbf{h}_{B}^{n+1}=\exp \left(f_{\boldsymbol{\theta}, \mathbf{s}}^{n}\left(\mathbf{h}_{A}^{n} ; \mathbf{u}\right)\right) \cdot \mathbf{h}_{B}^{n}+f_{\boldsymbol{\theta}, \mathbf{b}}^{n}\left(\mathbf{h}_{A}^{n} ; \mathbf{u}\right)
$$
其中，$\mathbf{h}^{n}=\left(\mathbf{h}_{A}^{n}, \mathbf{h}_{B}^{n}\right)$ 是对特征图的一个通道划分。如果 $\mathbf{h}_{A}^{n}$ 和 $\boldsymbol{y}$ 没有关系，那么这个式子的 Jacobian 矩阵是很容易计算的。而且其行列式的对数可以用 $\sum_{i j k} f_{\boldsymbol{\theta}, \mathbf{s}}^{n}\left(\mathbf{h}_{A}^{n} ; \mathbf{u}\right)_{i j k}$ 计算。这里的 $f_{\boldsymbol{\theta}, \mathbf{s}}^{n}, f_{\boldsymbol{\theta}, \mathbf{b}}^{n}$ 分别起到 scale 和 bias 的作用，它们可以是任意的网络结构，对它们而言不需要考虑可逆的问题。实际的做法是一个 conv-ReLU 模块降维至 64，然后接另一个 conv-ReLU 模块，输出仍然是 64 通道，在分别用两个不同的卷积层（64 通道）得到 scale 和 bias，和 Affine injector 一样。输入则是 $\mathbf{u}$ （需要 resize）和 $\mathbf{h}_A^n$ 的 concatenation 的结果。

这里最大的疑惑是 $\mathbf{h}_A^n$ 是怎么得到的？似乎这是个一个独立的变量，和 $\mathbf{u}$ 在计算上是相同的地位，$\mathbf{h}_B$ 才是向后传的核心。而且 $\mathbf{h}_A$ 在同一个 level 的计算中，其实没有发生任何变化。

**Split**

这个部分应该和 $\mathbf{h}_A, \mathbf{h}_B$ 的分离有关系。在一个 level 中，最后的 $\mathbf{h}_B$ 似乎就是隐变量 $\boldsymbol{z}$

**LR encoding network $g_{\boldsymbol{\theta}}$**

这个模块不要求可逆，所以任何可微的网络都可行。实际的做法是使用了 [47] 的网络结构，基于 Residual-in-Residual Dense Blocks (RRDB)，包含多个残差和dense skip connection，没有 BN 层。由于只是特征提取，所以去掉了最后的上采样层。为了保留所有的 LR image 的特征，将每个 RRDB 模块的激活值的 concatenation 作为这个模块的输出。

**总结**

Squeeze 模块用于不同 level 之间的下采样。为了避免生成时出现棋盘状的伪影，在 Squeeze 模块之后，用 Actnorm 和 1x1 Convolution 构成 Transition step。而 Actnorm, 1x1 Convolution, Affine injector, Conditional Affine Coupling 构成了 Conditional Flow Step，这个是 $f_{\boldsymbol{\theta}}^n$ 的主体部分。

#### 数据操纵

核心是对从 $\boldsymbol{y}$ 获得的 $\boldsymbol{z}$ 进行修改，然后再生成 SR 图像。$\boldsymbol{y}$ 和 $\boldsymbol{x}$ 之间不一定是一致的。具体的做法：

先从下列分布进行抽样得到 $\hat{\mu}, \hat{\sigma}$
$$
\hat{\mu}=\frac{1}{N} \sum_{i=1}^{N} z_{i} \sim \mathcal{N}\left(0, \frac{\tau}{N}\right), \hat{\sigma}^{2}=\frac{1}{N-1} \sum_{i=1}^{N}\left(z_{i}-\hat{\mu}\right)^{2} \sim \Gamma\left(\frac{N-1}{2}, \frac{2 \tau}{N-1}\right)
$$
然后计算：
$$
\hat{z}=\frac{\hat{\sigma}}{\tilde{\sigma}}(\tilde{z}-\tilde{\mu})+\hat{\mu}, \quad \forall \tilde{z} \in \tilde{\mathcal{Z}}
$$
其中 $\tilde{\mu},\tilde{\sigma}^2$ 是empirical mean and variance of the collection $\tilde{\mathcal{Z}}$，$\tilde{\mathcal{Z}}$ 则是从 $\boldsymbol{z}$ 中选择出部分的分量进行计算。选择的方式不同，对应不同的功能。文章中提到三种方式：

1. Global normalization：$\tilde{\mathcal{Z}}_{i j l}=\left\{\mathbf{z}_{i j k l}\right\}_{ijkl}$，整个隐变量空间
2. local normalization：$\tilde{\mathcal{Z}}_{i j l}=\left\{\mathbf{z}_{i j k l}\right\}_{k}$，每个 level 同一个位置的分量
3. Spatial normalization：$\tilde{\mathcal{Z}}_{i j l}=\left\{\mathbf{z}_{i j k l}\right\}_{ij}$，每个 level 每个 channel 的 map 上的分量。

最后用 $\hat{\boldsymbol{z}}$ 和 $\boldsymbol{x}$ 重建。



### 实验

#### 细节

每个 level 16 个 flow steps，Squeeze 之后有两个 Transition step。$g_{\boldsymbol{\theta}}$ 在一般图像的超分辨上使用了 23 个 RRDB，对于人脸图像，使用了 8 个。

Adam 优化器，lr = 5e-4，在 50%，75%，90%，95% 的迭代次数后减半。

对 RRDB 模块使用 L1 loss 进行预训练（200k iteration）。

SRFlow 网络训练 200k iteration。

时间消耗：5 天，gpu：单个 NVIDIA V100

数据集：HR resolution 是 160x160，对 CelebA 数据集做了裁剪。对于一般图像的数据集，使用了和 [47] 一样的数据集，800 DIV2k 和 2650 images from Flickr2K.

LR 图像生成：using the standard MATLAB bicubic kernel

训练技巧：加一些高斯噪声道 HR 图像上，可以帮助训练。

其他：因为 SRFlow 仅仅用了卷积，所以可以用 patch 训练，而生成完整的图像。



#### 消融实验

flow steps 的数量，channels 的数量的影响。（模型越大越好）

Analysis of the impact of the transitional linear ﬂow steps and the aﬃne image injector.

#### 应用和利用图形操纵的任务

**随机超分辨**

对于这个任务，甚至可以在隐变量的局部进行重新采样，以保证其他部分不变化。

![屏幕快照 2020-12-07 下午10.22.36](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-07 下午10.22.36.png)

**风格迁移**

用 source HR 和 LR 图像得到 $\boldsymbol{z}$， 然后结合 target 的 LR 图像得到风格迁移的图像。

![屏幕快照 2020-12-07 下午10.23.05](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-07 下午10.23.05.png)

可以看到眼睛、头发、额头的变化。

**图像内容内容迁移**

将 SR $\boldsymbol{y}$ 的一部分用另外的图像的一部分替换得到 $\tilde{\boldsymbol{y}}$，和 LR $\boldsymbol{x}$ 计算出 $\tilde{\boldsymbol{z}}$，对隐变量做 local normalization（只在修改的地方），然后重新生成。

![屏幕快照 2020-12-07 下午10.41.51](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-07 下午10.41.51.png)

**图像去噪和恢复**

用带噪声的图像和对应的 LR 图像计算出 $\tilde{\boldsymbol{z}}$，对隐变量做 spacial and local normalization，然后重新生成。



### 相关文献

关于超分辨任务的文献思路。

#### L2/L1 loss 的深度学习

[12] Dong, C., Loy, C.C., He, K., Tang, X.: Learning a deep convolutional network for image super-resolution. In: ECCV. pp. 184–199 (2014). https://doi.org/10.1007/978-3-319-10593-2_13

[13] Dong, C., Loy, C.C., He, K., Tang, X.: Image super-resolution using deep convolutional networks. TPAMI 38(2), 295–307 (2016)

[20] Kim, J., Kwon Lee, J., Mu Lee, K.: Accurate image super-resolution using very deep convolutional networks. In: CVPR (2016)

[22] Lai, W.S., Huang, J.B., Ahuja, N., Yang, M.H.: Deep laplacian pyramid networks for fast and accurate super-resolution. In: CVPR (2017)

[24] Lim, B., Son, S., Kim, H., Nah, S., Lee, K.M.: Enhanced deep residual networks for single image super-resolution. CVPR (2017)

#### GAN/perceptual loss

[54] Yu, X., Porikli, F.: Ultra-resolving face images by discriminative generative networks. In: ECCV. pp. 318–333 (2016). https://doi.org/10.1007/978-3-319-464541_20

[23] Ledig, C., Theis, L., Huszár, F., Caballero, J., Cunningham, A., Acosta, A., Aitken, A.P., Tejani, A., Totz, J., Wang, Z., et al.: Photo-realistic single image superresolution using a generative adversarial network. CVPR (2017)

[39] Sajjadi, M.S.M., Schölkopf, B., Hirsch, M.: Enhancenet: Single image superresolution through automated texture synthesis. In: IEEE International Conference on Computer Vision, ICCV 2017, Venice, Italy, October 22-29, 2017. pp. 4501–4510. IEEE Computer Society (2017). https://doi.org/10.1109/ICCV.2017.481

[2] Ahn, N., Kang, B., Sohn, K.A.: Image super-resolution via progressive cascading residual network. In: CVPR (2018)

[16] Haris, M., Shakhnarovich, G., Ukita, N.: Deep back-projection networks for superresolution. In: CVPR (2018)

[47] Wang, X., Yu, K., Wu, S., Gu, J., Liu, Y., Dong, C., Loy, C.C., Qiao, Y., Tang, X.: Esrgan: Enhanced super-resolution generative adversarial networks. ECCV (2018)

#### GAN-based stochastic SR

[4] Bahat, Y., Michaeli, T.: Explorable super resolution. In: CVPR (2020)

[8] Bühler, M.C., Romero, A., Timofte, R.: Deepsee: Deep disentangled semantic explorative extreme super-resolution. arXiv preprint arXiv:2004.04433 (2020)

[31] Menon, S., Damian, A., Hu, S., Ravi, N., Rudin, C.: Pulse: Self-supervised photo upsampling via latent space exploration of generative models. In: CVPR (2020)

#### one-image training

[41] Shocher, A., Cohen, N., Irani, M.: Zero-shot super-resolution using deep internal learning. In: CVPR (2018)

[6] Bell-Kligler, S., Shocher, A., Irani, M.: Blind superresolution kernel estimation using an internal-gan. In: NeurIPS. pp. 284–293 (2019), http://papers.nips.cc/paper/ 8321-blind-super-resolution-kernel-estimation-using-an-internal-gan

[40] Shaham, T.R., Dekel, T., Michaeli, T.: Singan: Learning a generative model from a single natural image. In: ICCV. pp. 4570–4580 (2019)

#### normalizing flow model

[38] Rezende, D.J., Mohamed, S.: Variational inference with normalizing ﬂows. In: Proceedings of the 32nd International Conference on Machine Learning, ICML 2015, Lille, France, 6-11 July 2015. pp. 1530–1538 (2015)

[10] Dinh, L., Krueger, D., Bengio, Y.: NICE: non-linear independent components estimation. In: 3rd International Conference on Learning Representations, ICLR 2015, San Diego, CA, USA, May 7-9, 2015, Workshop Track Proceedings (2015)

[11] Dinh, L., Sohl-Dickstein, J., Bengio, S.: Density estimation using real NVP. In: 5th International Conference on Learning Representations, ICLR 2017, Toulon, France, April 24-26, 2017, Conference Track Proceedings (2017)

[21] Kingma, D.P., Dhariwal, P.: Glow: Generative ﬂow with invertible 1x1 convolutions. In: Advances in Neural Information Processing Systems 31: Annual Conference on Neural Information Processing Systems 2018, NeurIPS 2018, 3-8 December 2018, Montréal, Canada. pp. 10236–10245 (2018)

[3] Ardizzone, L., Lüth, C., Kruse, J., Rother, C., Köthe, U.: Guided image generation with conditional invertible neural networks. CoRR abs/1907.02392 (2019), http: //arxiv.org/abs/1907.02392

[49] Winkler, C., Worrall, D.E., Hoogeboom, E., Welling, M.: Learning likelihoods with conditional normalizing ﬂows. arxiv abs/1912.00042 (2019), http://arxiv.org/ abs/1912.00042