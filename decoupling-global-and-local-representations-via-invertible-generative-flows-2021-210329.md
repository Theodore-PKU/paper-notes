# Decoupling Global and Local Representatins via Invertible Generative Flows

[TOC]

## 信息

Authors: Xuezhe Ma, Xiang Kong, Shanghang Zhang, Eduard Hovy

Years: ICLR 2021

Bibtex:

暂无

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/decoupling-global-and-local-representations-via-invertible-generative-flows.pdf)



## 概括

这篇文章算是一篇很不错的文章。作者的本意是结合 VAE 和 flow 模型的优点，克服两者的缺点。构建的模型就是 VAE 的 decoder 用 flow 模型表示。作者认为，VAE 模型适合获取图像的 global 信息，而 flow 模型适合获取图像的 local 信息，两者结合，就构成了图像的一种 global 和 local 的信息表示。VAE 的encoder将 x 映射到 z，获取的是 global 信息，在给定 z 的情况下，用一个flow模型将另一个隐变量 v 映射到 x，z 充当条件，v 实际上是 local 信息的表示。训练的时候，虽然作者没有特别明确地写出来，我个人猜测是 KL散度 + flow 模型的概率损失函数。z 在 flow 模型中，作为 coupling layer 的参数网络的输入的一部分。这有一点像 styleGAN，z 参与到生成的整个过程中。采样的时候，随机生成 z 和 v 之后就可以生成图像了，而计算概率，作者没有明确说明，但是我认为也是可以进行的，首先通过 encoder 得到一个 z（这里 encoder 仍然是计算 z 的均值和标准差），然后 x 在给定 z 的条件下，通过 flow 模型计算出对应的 v，然后就可以算概率了。

由于 z，v 的信息分离，这篇文章还可以实现图像的 switch 转换。

这个方法从效果上看虽然还比不上 GAN 模型，但是我认为还不错，是一个很好的尝试。好的文章就是思路简单清晰。



## 方法

这里具体说明这篇文章的相关细节。

整体的网络结构：

![屏幕快照 2021-03-30 下午3.44.04](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-30 下午3.44.04.png)

注意隐变量 z 在 decoder 中是一个条件。

本来 VAE 的损失函数是
$$
\begin{equation}
\mathcal{L}_{\text {elbo }}(\theta, \phi)=\mathrm{E}_{p(X)}\left[\mathrm{E}_{q_{\phi}(Z \mid X)}\left[-\log p_{\theta}(X \mid Z)\right]+\mathrm{KL}\left(q_{\phi}(Z \mid X) \| p_{\theta}(Z)\right)\right]
\end{equation}
$$
现在有了 flow 模型的 decoder，第一项应该会变成用 flow 模型计算的概率，涉及到 Jacobian 矩阵了。

**Encoder**

encoder 的输出是 z 的均值和方差。采用了 ResNet 的结构，因为只考虑 global 的信息，丢弃 local 信息，所以这个结构 make sense。并且最后 z 是用全连接层获得，是一个一维的向量。这里有一个初始化的技巧，让全连接层的参数都为 0，这样保证 z 最开始采样的时候是一个正态分布。

**Decoder**

flow 模型基本结构是 glow。并且对 glow 模型有一些修改。主要的修改地方是 1x1 卷积，因为生成的图像较大，作者要减少这部分参数，使用四种不同的 split 变量的方式。

![屏幕快照 2021-03-30 下午3.49.23](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-30 下午3.49.23.png)

coupling layer的形式，可以看到 z 是一个条件。
$$
\begin{equation}
\begin{aligned}
x_{a}, x_{b} &=\operatorname{split}(x) \\
y_{a} &=x_{a} \\
y_{b} &=\mathrm{s}\left(x_{a}, z\right) \odot x_{b}+\mathrm{b}\left(x_{a}, z\right) \\
y &=\operatorname{concat}\left(y_{a}, y_{b}\right)
\end{aligned}
\end{equation}
$$
参数网络的形式是：

![屏幕快照 2021-03-30 下午3.53.37](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-30 下午3.53.37.png)

这里的 + 用的将 FC 的一维结果直接加到 2D 上。这里其实我有一些疑问，这个做法虽然和 styleGAN 很像，但是不太能理解参数 s 和 b 的意义了。

**实验**

在实验部分，作者也考虑的 dequantization，不过用的是最简单加随机噪声的方法。数据集用了好几个，LSUN 是 128分辨率，CelebA-HQ 是 256 分辨率。

既然 z 和 v 是不同信息，作者就考虑了一个不同图像的 z 和 v 的插值：
$$
\begin{equation}
\begin{aligned}
h(z) &=(1-\alpha) z_{1}+\alpha z_{2} \\
h(v) &=(1-\beta) v_{1}+\beta v_{2}
\end{aligned}
\end{equation}
$$
switch 的效果，我觉得很类似于 styleGAN：

![屏幕快照 2021-03-30 下午3.44.18](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-30 下午3.44.18.png)



## 反思

这篇文章我认为写的很好，思路简洁清晰，效果也还不错。这种把 VAE 和 flow 模型结合的方式我觉得很有启发性，因为考虑 VAE 和 flow 模型的优缺点。不过也有一些疑问，flow 模型当中 z 的信息参与到 x 和 v 之间的变换，总觉得有一些怪怪的。这篇文章的方法可能会成为自己的想法的一个基础。

图像的表示、flow 模型的优劣，这些问题都是很核心的问题。



## 参考文献

Siddharth Agrawal and Ambedkar Dukkipati. Deep variational inference without pixel-wise reconstruction. arXiv preprint arXiv:1611.05209, 2016.

Alexander A Alemi, Ian Fischer, Joshua V Dillon, and Kevin Murphy. Deep variational information bottleneck. In International Conference on Learning Representations (ICLR), 2017.

Tian Qi Chen, Xuechen Li, Roger B Grosse, and David K Duvenaud. Isolating sources of disentanglement in variational autoencoders. In Advances in Neural Information Processing Systems, pp. 2610–2620, 2018.

Xi Chen, Diederik P Kingma, Tim Salimans, Yan Duan, Prafulla Dhariwal, John Schulman, Ilya Sutskever, and Pieter Abbeel. Variational lossy autoencoder. In Proceedings of the 5th International Conference on Learning Representations (ICLR-2017), Toulon, France, April 2017a.

Irina Higgins, Loic Matthey, Arka Pal, Christopher Burgess, Xavier Glorot, Matthew Botvinick, Shakir Mohamed, and Alexander Lerchner. beta-vae: Learning basic visual concepts with a constrained variational framework. In Proceedings of the 5th International Conference on Learning Representations (ICLR-2017), Toulon, France, April 2017.

Chin-Wei Huang, Laurent Dinh, and Aaron Courville. Augmented normalizing ﬂows: Bridging the gap between generative ﬂows and latent variable models. arXiv preprint arXiv:2002.07101, 2020.

Hyunjik Kim and Andriy Mnih. Disentangling by factorising. In International Conference on Machine Learning, pp. 2649–2658, 2018.

Xuezhe Ma, Xiang Kong, Shanghang Zhang, and Eduard Hovy. Macow: Masked convolutional generative ﬂow. In Advances in Neural Information Processing Systems 33. 2019a.

Emile Mathieu, Tom Rainforth, N Siddharth, and Yee Whye Teh. Disentangling disentanglement in variational autoencoders. In International Conference on Machine Learning, pp. 4402–4412, 2019. 

Michael F Mathieu, Junbo Jake Zhao, Junbo Zhao, Aditya Ramesh, Pablo Sprechmann, and Yann LeCun. Disentangling factors of variation in deep representation using adversarial training. In Advances in neural information processing systems, pp. 5040–5048, 2016.

Rogan Morrow and Wei-Chen Chiu. Variational autoencoders with normalizing ﬂow decoders. OpenReview, 2019.

George Papamakarios, Eric Nalisnick, Danilo Jimenez Rezende, Shakir Mohamed, and Balaji Lakshminarayanan. Normalizing ﬂows for probabilistic modeling and inference. arXiv preprint arXiv:1912.02762, 2019.

