# Adversarial Autoencoders

[TOC]

### 信息

Authors: Alireza Makhzani, Jonathon Shlens & Navdeep Jaitly, Ian Goodfellow and Brendan Frey

University of Toronto, Google Brain and OpenAI

arXiv 2015 应该后来被接收了

```
@article{makhzani2015adversarial,
  title={Adversarial autoencoders},
  author={Makhzani, Alireza and Shlens, Jonathon and Jaitly, Navdeep and Goodfellow, Ian and Frey, Brendan},
  journal={arXiv preprint arXiv:1511.05644},
  year={2015}
}
```

引用：1500+

[Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/adversarial-autoencoders.pdf)



### 概括

这篇文章概括起来其实非常简单，将 VAE 模型中的 KL 散度的计算替换成了对抗损失函数。KL 散度本身是为了使得后验分布逼近先验分布，这个和对抗损失函数的训练目标是一致的，所以这种替换也比较合理。

这篇文章较长，因为还考虑了各种应用。作者把 AAE 模型做了一些拓展，考虑了 label 信息的。



### 方法

基本方法就是在 AE 模型的编码部分，增加一个判别器，用对抗训练的方式使得编码的性质和给定的先验分布的性质一样。因此训练的时候包含两个步骤，一个步骤是重建，一个步骤是正则。正则部分就是训练 GAN（生成器是 AE 的编码器）。

![屏幕快照 2020-12-26 下午7.06.50](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-26 下午7.06.50.png)

这片文章考虑了三种不同的编码器。

1. 确定型。每个 $x$ 对应一个编码 $z$，$z$ 的随机性完全来源于 $x$

2. 高斯分布。$z|x$ 是一个高斯分布。这个和 VAE 模型是一样的

3. 一般后验概率逼近。$z$ 的计算不仅仅由 $x$ 决定，另一个随机变量（噪声 $\eta$）也会参与。
   $$
   \begin{equation}
   q(\mathbf{z} \mid \mathbf{x}, \eta)=\delta(\mathbf{z}-f(\mathbf{x}, \eta))
   \end{equation}
   $$
   在第三种编码器中，$z$ 的随机性来源是 $x$ 和 $\eta$

作者对这三种编码器的分析：第一种是不合适的，因为 $x$ 的分布局限在训练集上，这种编码可能会让编码 $z$ 不够平滑。但是通过大量的实验，作者发现训练之后他们在测试集上得到了相似的 $z|x$，所以最后都采用了第一种编码器。（那这个分析不是自己打自己脸吗）

作者还分析了和 VAE 的差别。VAE 的推导是：
$$
\begin{equation}
\begin{aligned}
E_{\mathbf{x} \sim p_{d}(\mathbf{x})}[-\log p(\mathbf{x})] &<E_{\mathbf{x}}\left[\mathrm{E}_{q(\mathbf{z} \mid \mathbf{x})}[-\log (p(\mathbf{x} \mid \mathbf{z})]]+E_{\mathbf{x}}[\operatorname{KL}(q(\mathbf{z} \mid \mathbf{x}) \| p(\mathbf{z}))]\right.\\
&=E_{\mathbf{x}}\left[\mathrm{E}_{q(\mathbf{z} \mid \mathbf{x})}[-\log p(\mathbf{x} \mid \mathbf{z})]\right]-E_{\mathbf{x}}[H(q(\mathbf{z} \mid \mathbf{x}))]+E_{q(\mathbf{z})}[-\log p(\mathbf{z})] \\
&\left.=E_{\mathbf{x}}\left[\mathrm{E}_{q(\mathbf{z} \mid \mathbf{x})}[-\log p(\mathbf{x} \mid \mathbf{z})]\right]-E_{\mathbf{x}}\left[\sum_{i} \log \sigma_{i}(\mathbf{x})\right)\right]+E_{q(\mathbf{z})}[-\log p(\mathbf{z})]+\text { const. } \\
&=\text { Reconstruction }-\text { Entropy }+\operatorname{Cross} \operatorname{Entropy}(q(\mathbf{z}), p(\mathbf{z}))
\end{aligned}
\end{equation}
$$
实际上，AAE 就是替换了其中的正则项部分。

作者试图从编码的角度来分析两者的差异，并指出 AAE 的编码更好地捕捉了流形的特点。这个部分我持保留意见。当然 AAE 的一个好处是不需要知道先验分布，只要可以从先验分布中抽样就可以了。但是这种做法分析起来，假设每次只抽样一个数据，就好像强迫编码落在某个区域上而已。而这个区域就是先验分布的概率最高的地方。

![屏幕快照 2020-12-31 下午8.00.02](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-31 下午8.00.02.png)



### 拓展

作者提出的第一个拓展是将 label 信息考虑进来，进一步控制编码的分布。以 MNIST 任务为例。作者把混合高斯分布分成了十个 mode，每个mode对应一个类。编码在判别的时候，输入还包括类别的 one-hot 向量（这个向量有十一类，其中一类表示 label 未知）。这个拓展可以理解成每一类数字的分布是局限在某个 mode 上的（似乎 one-hot 向量没有也没关系？），编码器得到的编码会添加一个由输入决定的类别的 one-hot 向量。因为对抗训练就会让这个编码接近于给定的 mode。我感觉这有一点条件分布的味道。

![屏幕快照 2020-12-26 下午7.34.30](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-26 下午7.34.30.png)

第二个拓展考虑将 label 和 style 分离，所以在编码部分增加了一个监督学习的部分。只有 stlye 参与判别。而 decoder 会结合 label 和 style 得到输出。

![屏幕快照 2020-12-26 下午7.46.41](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-26 下午7.46.41.png)

作者还考虑半监督训练的拓展，和第二个类似，只是对于 label y，也增加了个一个判别器。

![屏幕快照 2020-12-26 下午7.54.27](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-26 下午7.54.27.png)

在之后还有聚类什么的，就不看了。这篇文章看到这里就足够了。