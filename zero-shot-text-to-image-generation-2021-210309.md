# Zero-Shot Text-to-Image Generation

[TOC]

## 信息

Authors: Aditya Ramesh, Mikhail Pavlov, Gabriel Goh, Scott Gray, Chelsea Voss, Alec Radford, Mark Chen, Ilya Sutskever

Year: 2021, arXiv

Bibtex:

```latex
@article{ramesh2021zero,
  title={Zero-Shot Text-to-Image Generation},
  author={Ramesh, Aditya and Pavlov, Mikhail and Goh, Gabriel and Gray, Scott and Voss, Chelsea and Radford, Alec and Chen, Mark and Sutskever, Ilya},
  journal={arXiv preprint arXiv:2102.12092},
  year={2021}
}
```

[Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/zero-shot-text-to-image-generation.pdf)



## Code

https://github.com/openai/DALL-E



## 概括

这篇文章介绍的是 OpenAI 的 DALL-E 模型的相关内容。主要包含了模型设计、训练方法、数据收集处理、分布式训练等，还有其他的一些实现细节。

对于我们而言，DALL-E 这样的模型是无法实现的，所以关注的重点是模型设计的思想。至于数据处理、分布式训练以及其他细节就不关注了。

DALL-E 模型要解决的问题是从文字生成图像。为了实现这个目的，作者构建了两个主要模型，一个是 dVAE 模型，这里的 d 表示的离散；另一个是 Transformer 模型。整个模型包含 120 亿参数，使用了 2.5 亿个数据对。



## 方法

核心思想是用 dVAE 模型获得图像的 token 表示，用 Transformer 模型对图像 token 和文字 token 的联合分布进行建模。但是，这里还有一些细节没有弄清楚。

假设图像是 $x$, 文字是 $y$, 图像 token 是 $z$, 这里没有提到文字的 token，但实际上似乎 $y$ 就是文字的 token。

作者考虑的 $x, y, z$ 的联合分布，使用如下的分解：
$$
\begin{equation}
\begin{aligned}
\ln p_{\theta, \psi}(x, y) \geqslant \mathbb{E}_{z \sim q_{\phi}(z \mid x)} \left(\ln p_{\theta}(x \mid y, z)- \beta D_{\mathrm{KL}}\left(q_{\phi}(y, z \mid x), p_{\psi}(y, z)\right)\right)
\end{aligned}
\end{equation}
$$
这个式子的本质和 VAE 的理论框架是类似的，要计算图像和文字对的概率，借助隐变量 $z$ 来实现。但是这里的先验分布 $p(y, z)$ 实际上是未知的。为了实现这一点，作者使用了两阶段训练的策略。

第一个阶段是训练一个 dVAE，这个训练相当于 (1) 式中将 $y$ 的部分去掉。这就变成一个单纯的 VAE 模型了。dVAE 的意思是说隐变量 $z$ 的分布建模是离散的。之所以这么考虑，我个人的猜测是因为要和 $y$ 文本结合起来，使用 Transformer 模型。具体来说，隐变量，也就是图像 token $z$ 是一个 32x32 的 grid，每个格点上是有 8192 个可能的值。这里的 8192 有点像类别变量，因此是离散的，并不是用 8192 维向量来表示。因为去掉了 $y$，所以 (1) 式就和普通的 VAE 框架是一样的。$z$ 本身的先验分布式一个均匀的类别分布。由于 $z$ 是离散的，因此计算 KL 散度的时候，需要特殊处理，作者在文章中说使用了 gumbel-softmax relaxation 的技巧来优化（因为重参数技巧是不能用的）

个人理解：这个 dVAE 更像是将 8x8 的 patch 表达成一个类似词向量一样的值，或者说类似于字典的 key 值。重建的时候，这些 key 值会在 decoder 中转换为实际的图像 patch。

我研究了一下 dVAE 的代码，并没有特别之处。encoder 和 decoder 就是普通的卷积、残差等等。

当 dVAE 训练完之后，就要训练 $p(y, z)$ 的联合分布（第二阶段）。这个联合分布，用一个 Transformer 表示，这个 transformer 非常大，但是不太清楚输出是什么样的。目前理解的是，输入是图像 token $z$ 和文字的 token 的 concatenation，文字的 token 的词库大小是 16384，最多 256 token（也就是说句子的长度最多 256）。至于输出，目前不清楚，但既然要 Transformer 表示分布，输出难道是概率值吗？。回到 (1) 式中，当 dVAE 训练完之后，$q_{\phi}(y,z|x)$ 是已知的。这里作者的假设是 $y$ is conditionally independent of $x$ given $z$。我只能理解为有了 dVAE 的 encoder，这个就能直接算了。另外，$p_\theta(x|y, z)$ 是一个我目前无法理解的点，$y$ 是如何参与到 $x$ 的生成，如果只有 $z$，那就是 dVAE 的 decoder，但是现在有了 $y$，如何产生 $z$ 是我无法理解的。

有一种解释是在第二个阶段里面，其实要训练的仅仅是 $p_{\psi}(y, z)$，所以实际上仅仅训练的是一个 KL 散度。之所以这样解释的原因是，DALL-E 的生成 sample 的方法似乎是根据这个 Transformer，在已知 $y$ 的情况下，找到概率值最大的一些 $z$，然后用这些 $z$ 直接生成图像。如果是这样，那么确实不用考虑 $p_{\theta}(x|y, z)$. 训练的时候只要先根据 $x$ sample 出 $z$，然后计算 KL 散度就可以了。



## 反思

其实这篇文章 VAE 的处理是比较简单的，但是为了计算 $y, z$ 的联合分布可以表示成 Transformer 的形式，采用了 token 形式的隐变量。实际上这种做法会直接导致重建的图像其实并不能完全还原原始图像，更像是一种压缩。而这在论文中的图示也验证了这一点。dVAE 重建的图像质量并不高。这个质量更接近于下采样 8x 后的超分辨的结果。这种做法和我们要实现的真正强大的图像生成模型，目标是不一致的。

但是这篇文章提到 $y,z$ 先验分布也是通过训练得到，这一点我感觉有一些启发，似乎也可以用于 inverse problem。虽然 $y$ 和 $z$ 在文本生成图像任务上，具有很大的差异，而在 inverse problem 上，如果 $y$ 也是图像，$y$ 和 $z$ 的差异就小很多了。