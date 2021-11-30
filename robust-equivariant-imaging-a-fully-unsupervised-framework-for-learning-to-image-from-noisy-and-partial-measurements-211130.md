# Robust Equivariant Imaging: a fully unsupervised framework for learning to image from noisy and partial measurements

[TOC]

## 信息

作者：Dongdong Chen, Julián Tachella, Mike E. Davies

年份：2021

期刊会议：arXiv

Bibtex：暂无

code：https://github.com/edongdongchen/REI

tags：mri; ct; reconstruction; determined; network mapping; 

key words：unsupervised learning; SURE; equivariant imaging; robust to noise;

有部分参考论文可能需要补充阅读。



## 概括

详读。

这篇文章研究的是求解 inverse problem 的一种无监督方法。作者之前提出了 EI，equivariant imaging 这个无监督方法来求解 inverse problem，但是无法处理 noisy 测量值的情形。因此作者结合了 SURE 来解决 noisy 测量值问题。取得了比较好的效果，这个方法对于噪声比较鲁棒。EI 这个方法，用一句话概括就是：data consistency + 重建系统的不变性。这里的不变性的含义是说，如果重建系统的输入是变换后的信号的测量值，那么重建结果应该是变换后的信号。但是这个不变性对于无监督学习的情形，由于我们没有这个信号的数据，因此作者直接用了重建结果（也就是网络输出）来替代，这可以看成是一种伪数据（伪标签）的做法。对于 noisy 测量值，实际上主要影响的是 data consistency，而 data consistency 正是可以利用 SURE 的地方，因为 SURE 解决的就是 MSE loss 在 noisy label 下的一个无偏估计。更详细的内容，参考方法的部分。



## 方法

这篇文章的主要方法是将 EI（这是作者自己在 ICCV 上的一篇论文提出的无监督学习的方法）和 SURE 结合，目的是解决 EI 对噪声的鲁棒性。EI 是一种解决逆问题的无监督学习方法，此前考虑的是无噪声的测量值，当噪声存在时，会导致模型的效果下降很多。SURE 则是一个处理 noise label 的一种方法。作者将两者结合，提出了 REI（robust EI）。和 EI 相比，在有噪声的情况效果提升了很多。

我们先介绍 EI，然后介绍 SURE，最后介绍两者如何结合。

EI 方法的想法非常简单，就是重建系统必须满足某些不变性。假设重建系统（网络）是 $f_{\theta}$，信号是 $x$, 测量值是 $y$，测量系统是 $A$。那么假设 $x$ 已知，一个好的重建系统应该满足：
$$
T_{g} f_{\theta} (A(x)) = f_{\theta}(A(T_{g}x))
$$
$T_{g}$ 就是某个变换，比如旋转和平移。这个性质可以理解为：变换后的测量值重建的结果应该和重建结果的变换是一致的。

作者在论文中有一个分析，我认为是不对的，具体可以看论文 2.1 节的 EI 部分。

除了这个约束，一个常见的约束是 data consistency，因此两者结合就是 EI。不过在不变性的约束中，我们没有 $x$，所以作者替换成了重建系统重建的结果（可以看成是一个伪标签和伪输入）。最后的损失函数就可以写成：
$$
\mathcal{L}_{\mathrm{EI}}(\theta)=\mathcal{L}_{\mathrm{MC}}(\theta)+\alpha \mathcal{L}_{\mathrm{EQ}}(\theta) \\
\mathcal{L}_{\mathrm{MC}}(\theta)=\sum_{i=1}^{N} \frac{1}{m}\left\|y_{i}-A\left(f_{\theta}\left(y_{i}\right)\right)\right\|^{2} \\
\mathcal{L}_{\mathrm{EQ}}(\theta)=\sum_{i=1}^{N} \sum_{g=1}^{|\mathcal{G}|} \frac{1}{|\mathcal{G}| n}\left\|T_{g} f_{\theta}\left(y_{i}\right)-f_{\theta}\left(A\left(T_{g} f_{\theta}\left(y_{i}\right)\right)\right)\right\|^{2}
$$
MC 表示 data consistency，EQ 表示 不变性。很显然，这个约束无法保证重建系统一定是最合理的。EQ 部分实际上可以理解成，先重建出一个结果，然后当做是一个伪数据，那么这个伪数据的增广训练就是 EQ 的部分。

SURE 是一个去噪的无监督方法。相当于有监督训练的 MSE loss 的无偏估计。更详细的表述可以看论文，大概的含义就是：
$$
\mathcal{L}_{\mathrm{MSE}}(\theta)=\frac{1}{m} \sum_{i=1}^{N}\left\|u_{i}-h_{\theta}\left(y_{i}\right)\right\|_{2}^{2}
$$
可以用
$$
\mathcal{L}_{\mathrm{SURE}}(\theta)=\sum_{i=1}^{N} \frac{1}{m}\left\|y_{i}-h_{\theta}\left(y_{i}\right)\right\|^{2}-\sigma^{2}+\frac{2 \sigma^{2}}{m} \nabla \cdot h_{\theta}\left(y_{i}\right)
$$
来替代。这里的 $u_i$ 就是 clean label，当它不知道的时候，如何用 noise input 来训练。但是 SURE 的一个问题是 $\nabla \cdot h_{\theta}\left(y_{i}\right)$ 不好计算，通常大家会用下面的方法来估计这一项：
$$
\nabla \cdot h_{\theta}(y) \approx \frac{1}{\tau} b^{\top}\left(h_{\theta}(y+\tau b)-h_{\theta}(y)\right), b \sim \mathcal{N}(0, 1)
$$
$\tau$ 是一个很小的超参数。

EI 只用到了测量值 $y$ 来训练，如果测量值有噪声，训练的结果就会变差，而 SURE 就可以解决这个问题。

对于 MC 的部分，直接应用 SURE 的方法就可以了。对于 EQ 的部分，只要在构造伪输入的时候对重建结果增加噪声就行。$A\left(T_{g} f_{\theta}\left(y_{i}\right)\right)$ 变成 $A\left(T_{g} f_{\theta}\left(y_{i}\right)\right) + \epsilon$。作者实际考虑了不同的噪声（不同的噪声对应不同的 SURE 的计算形式，我们刚才给出的仅仅是高斯噪声），包括高斯、泊松、高斯泊松混合。

只有 MC 的部分真正涉及了 SURE，EQ 的部分只是保证了重建系统的一致性（输入一定是带噪声的）。具体的公式可以看论文。



## 实验

作者的实验有三个任务，MRI 重建、inpaiting 和 sparse view CT 重建。MRI 重建的数据集是 fastMRI，他们应该只用了 single coil 的数据，并且图像的 size 是 320x320. 另外两个任务的数据集不是特别在意，不过 sparse view CT 的数据集，以后可能也会用上，我们这里先记录一下：

> Kenneth Clark, Bruce Vendt, Kirk Smith, John Freymann, Justin Kirby, Paul Koppel, Stephen Moore, Stanley Phillips, David Mafﬁtt, Michael Pringle, et al. The cancer imaging archive (TCIA): maintaining and operating a public information repository. Journal of digital imaging, 26(6):10451057, 2013. 7

如果以后需要考虑 sparse view CT 的方法，也许可以使用这个公开数据集。

作者的实验主要是针对不同的任务，测量值施加不同类型的噪声，MRI 是高斯，CT 是泊松。然后比较直接用 EI 和 REI 的结果。作为对比，还有监督学习的结果。不过监督学习在这里是很简单的，仅仅是相同的 mapping 网络，使用 MSE 来训练。

结果显示，REI 在有噪声的情况下比 EI 好很多。但还是比监督学习差一些。

作者的消融实验有两个，一个是不同 loss 的组合，一个是超参数 $\alpha$.



## 反思

这篇文章是无监督学习，我们不是特别关心这类方法。不过这篇文章的效果还是可以的。但是我认为这个无监督方法无法保证学习的结果就是好的，仅仅可以看成是利用了一些额外的约束来训练网络，对于训练的结果没有任何保证。有额外的约束总是有利于训练出一个还可以的结果。

这篇文章的约束就是旋转、平移的不变性。这个不变性对应的损失函数，实际上可以看成是一种增广训练，只不过使用的 label 和 input 都是伪标签（或者说是利用 mapping 网络本身得到的结果）。这种约束是合理的，但是无法推出解是一定好的，只能说是解的一种性质。不过这种增广的约束，对于监督学习仍然是成立的，是否也可以考虑在监督学习中加入这个内容呢？另外，虽然没有理论保证，但是这背后是否可以进行更多的理论分析？这是一个值得探讨的问题。



## 参考论文

Ashish Bora, Eric Price, and Alexandros G Dimakis. AmbientGAN: Generative models from lossy measurements. In International Conference on Learning Representations, 2018. 关于 noise 测量值的无监督训练方法。

Elena Celledoni, Matthias Joachim Ehrhardt, Christian Etmann, Brynjulf Owren, Carola-Bibiane Schönlieb, and Ferdia Sherry. Equivariant neural networks for inverse problems. Inverse Problems, 37(8):085006, 2021. 这篇文章提出了一个类似的利用不变性的逆问题求解方法。

Dongdong Chen, Juli´an Tachella, and Mike E Davies. Equivariant imaging: Learning beyond the range space. In Proceedings of the International Conference on Computer Vision (ICCV), 2021. 这篇文章提出了 EI 方法。

Chunli Guo and Mike E. Davies. Near optimal compressed sensing without priors: Parametric sure approximate message passing. IEEE Transactions on Signal Processing, 63(8):2130–2141, 2015. 压缩感知的无监督方法。

Arthur Pajot, Emmanuel de Bezenac, and Patrick Gallinari. Unsupervised adversarial image reconstruction. In International Conference on Learning Representations, 2019. 无监督方法

Charles M Stein. Estimation of the mean of a multivariate normal distribution. The annals of Statistics, pages 11351151, 1981. 这篇文章提出了 SURE 方法。

Magauiya Zhussip, Shakarim Soltanayev, and Se Young Chun. Extending Stein’s unbiased risk estimator to train deep denoisers with correlated pairs of noisy images. Advances in neural information processing systems, 32:14651475, 2019. 这是一篇应用 SURE 方法的文章。