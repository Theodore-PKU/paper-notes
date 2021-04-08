# Latent Normalizing Flows for Many-many Cross-domain Mappings

[TOC]

## 信息

Authors: Shweta Mahajan, Iryna Gurevych, Stefan Roth

Year: ICLR 2020

Bibtex:

```
@article{mahajan2020latent,
  title={Latent normalizing flows for many-to-many cross-domain mappings},
  author={Mahajan, Shweta and Gurevych, Iryna and Roth, Stefan},
  journal={arXiv preprint arXiv:2002.06661},
  year={2020}
}
```

cite: 5



## 概括

这篇文章其实并没有对 flow 模型有什么改进，它研究的问题是图像和文本之间的映射关系，flow 模型在这里是充当分布变换的一个媒介。我认为这篇文章最主要内容在于不同 domain 之间的联系的建立方式。这篇文章的基本框架是 VAE 框架，对于隐变量的建模采取了比较特殊的结构，并且引入 flow 模型。简单来说，不同 domain 的隐变量分成了两个部分，一个是 domain-specific 信息，一个是 share 信息。基于这个划分，VAE 的损失函数等需要有重新的推导。而 domain-specific 的隐变量的先验分布使用了 flow 模型。两个 domain 的 share 隐变量之间也用了一个 flow 模型。

这篇文章最大的问题在于后验分布的建模是什么样的，完全没有说清楚。不过我也不太关系这个部分。总之这篇文章只需要在意作者的隐变量建模方式就可以了。



## 方法

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-07 下午9.15.28.png" alt="屏幕快照 2021-04-07 下午9.15.28" style="zoom: 67%;" />

这个图是一个简单的图示，通常在这种问题上，两个域使用了相同的隐变量空间，但是这篇文章使用了不同的隐变量。

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-08 下午2.16.30.png" alt="屏幕快照 2021-04-08 下午2.16.30" style="zoom:50%;" />

这张图则给出了更详细的细节，这里下标 v 表示图像，t 表示文字。$z_s$ 表示 share 信息，可以注意到，两个域的 share 信息之间有一个 flow 模型。而 domain-specific 信息 $z_{v}^{\prime}$ 和 $z_{t}^{\prime}$ 的先验分布则是 condition on $z_s$，并且是一个 flow 模型。

下面对公式进行分析。

首先是基本的 ELBO 公式：
$$
\log p_{\mu}\left(x_{t}, x_{v}\right) \geq \mathbb{E}_{q_{\theta}\left(z \mid x_{t}, x_{v}\right)}\left[\log p_{\mu}\left(x_{t}, x_{v} \mid z\right)\right]+\mathbb{E}_{q_{\theta}\left(z \mid x_{t}, x_{v}\right)}\left[\log p_{\phi}(z)-\log q_{\theta}\left(z \mid x_{t}, x_{v}\right)\right]
$$
然后我们考虑 $z$ 的分解。$z_v = [z_s, z_v^{\prime}], z_t = [z_s, z_t^{\prime}]$，那么 $z = [z_s, z_v^{\prime}, z_t^{\prime}]$。

考虑的两种 domain，因此 (1) 的第一项重建项就变成：
$$
\mathbb{E}_{q_{\theta}\left(z_{s}, z_{t}^{\prime}, z_{v}^{\prime} \mid x_{t}, x_{v}\right)}\left[\log p_{\mu}\left(x_{t} \mid z_{s}, z_{t}^{\prime}, z_{v}^{\prime}\right)+\log p_{\mu}\left(x_{v} \mid z_{s}, z_{t}^{\prime}, z_{v}^{\prime}\right)\right]
$$
利用两种 domain 的关系，和 share 信息、specific 信息的分离，我们有下列的分解：
$$
\log q_{\theta}\left(z_{s}, z_{t}^{\prime}, z_{v}^{\prime} \mid x_{t}, x_{v}\right)=\log q_{\theta_{1}}\left(z_{s} \mid x_{t}, x_{v}\right)+\log q_{\theta_{2}}\left(z_{t}^{\prime} \mid x_{t}, z_{s}\right)+\log q_{\theta_{3}}\left(z_{v}^{\prime} \mid x_{v}, z_{s}\right)
$$
利用 (3) 式，(2) 就变成：
$$
\mathbb{E}_{q_{\theta_{1}}\left(z_{s} \mid x_{t}, x_{v}\right) q_{\theta_{2}}\left(z_{t}^{\prime} \mid x_{t}, z_{s}\right)}\left[\log p_{\mu}\left(x_{t} \mid z_{s}, z_{t}^{\prime}\right)\right]+\mathbb{E}_{q_{\theta_{1}}\left(z_{s} \mid x_{t}, x_{v}\right) q_{\theta_{3}}\left(z_{v}^{\prime} \mid x_{v}, z_{s}\right)}\left[\log p_{\mu}\left(x_{v} \mid z_{s}, z_{v}^{\prime}\right)\right]
$$
因此重建的时候是用各自的隐变量来重建各自的信号。只不过对于 specific 的隐变量，需要用不同的模型来刻画。

接下来考虑 (1) 的第二项，也就是 KL 项。同样利用 (3) 的分解形式，可以拆成下面的形式。
$$
\begin{aligned}
& D_{\mathrm{KL}}\left(q_{\theta}\left(z_{s}, z_{t}^{\prime}, z_{v}^{\prime} \mid x_{t}, x_{v}\right)\right. \left.\| p_{\phi}\left(z_{s}, z_{t}^{\prime}, z_{v}^{\prime}\right)\right)=D_{\mathrm{KL}}\left(q_{\theta_{1}}\left(z_{s} \mid x_{t}, x_{v}\right) \| p_{\phi_{s}}\left(z_{s}\right)\right)+\\
& D_{\mathrm{KL}}\left(q_{\theta_{2}}\left(z_{t}^{\prime} \mid x_{t}, z_{s}\right) \| p_{\phi_{t}}\left(z_{t}^{\prime} \mid z_{s}\right)\right)+D_{\mathrm{KL}}\left(q_{\theta_{3}}\left(z_{v}^{\prime} \mid x_{v}, z_{s}\right) \| p_{\phi_{v}}\left(z_{v}^{\prime} \mid z_{s}\right)\right)
\end{aligned}
$$
这样就完成了整个建模的结果。

$p_{\phi_{s}}\left(z_{s}\right)$ 最后使用的是均匀分布。$p_{\phi_{t}}\left(z_{t}^{\prime} \mid z_{s}\right)$ 和 $p_{\phi_{v}}\left(z_{v}^{\prime} \mid z_{s}\right)$ 使用的 flow 模型，如图2所示那样。计算对数概率的时候要用的概率变换公式。

最后要说的一个问题是，如何保证两个 domain 产生的 share 信息是一致，因为在图2中，作者使用了一个 flow 模型连接两个 share 隐变量，所以实际计算的时候作者还试图让两个 $z_s$ 用 MSE 做监督学习。

总之，这篇文章很多细节没说清楚。至于实验，我就不管了。这篇文章只能说算是一个应用，并不能当作是一个 flow 模型的改进。



## 参考文献

Conor Durkan, Artur Bekasov, Iain Murray, and George Papamakarios. Neural spline ﬂows. In Advances in Neural Information Processing Systems, pages 7509–7520, 2019.

Wei Ping, Kainan Peng, Kexin Zhao, and Zhao Song. Waveﬂow: A compact ﬂow-based model for raw audio. In International Conference on Machine Learning, 2020.

Lynton Ardizzone, Jakob Kruse, Carsten Rother, and Ullrich K¨othe. Analyzing inverse problems with invertible neural networks. In ICLR, 2019.

Jens Behrmann, Will Grathwohl, Ricky T. Q. Chen, David Duvenaud, and J¨orn-Henrik Jacobsen. Invertible residual networks. In ICML, 2019.

Apratim Bhattacharyya, Michael Hanselmann, Mario Fritz, Bernt Schiele, and Christoph-Nikolas Straehle. Conditional ﬂow variational autoencoders for structured sequence prediction. In BDL@NeurIPS, 2019.

Xi Chen, Diederik P. Kingma, Tim Salimans, Yan Duan, Prafulla Dhariwal, John Schulman, Ilya Sutskever, and Pieter Abbeel. Variational lossy autoencoder. In ICLR, 2017.

A¨aron van den Oord, Oriol Vinyals, and Koray Kavukcuoglu. Neural discrete representation learning. In NeurIPS, 2017.