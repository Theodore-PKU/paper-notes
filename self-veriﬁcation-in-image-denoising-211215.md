# Self-Veriﬁcation in Image Denoising

[TOC]

## 信息

作者：Huangxing Lin, Yihong Zhuang, Delu Zeng, Yue Huang, Xinghao Ding, John Paisley

年份：2021

期刊会议：arXiv

Bibtex:

```latex
@article{lin2021self,
  title={Self-Verification in Image Denoising},
  author={Lin, Huangxing and Zhuang, Yihong and Zeng, Delu and Huang, Yue and Ding, Xinghao and Paisley, John},
  journal={arXiv preprint arXiv:2111.00666},
  year={2021}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/self-veriﬁcation-in-image-denoising.pdf)



## 概括

通读。

这篇文章研究的是自监督去噪任务。作者提出的方法，虽然从论文的讲述上来看，是从 DIP 出发，但实际上最后演化的形式我认为和 DIP 已经毫无关系了。另外 DIP 是在一个 image 上训练，这篇文章则是在整个 noise dataset 上训练。这篇文章的自监督学习思路和 Noise2Noise 这一类不同，也和 Noise2Score/SURE 不同，有点类似于 EM 算法，或者说是一种逐步近似的算法。作者提出了一个所谓的正则项，实际上是最后的优化目标。这个优化目标可以理解成从噪声图片构造出另一个噪声图片，这两个噪声图片经过网络去噪后的结果应该相同。作者把这个优化目标分解成了一个类似 EM 算法的形式。我尝试用定理一去分析，但是无法从理论上分析出一些有意思的结论。不过这篇文章提出的方法确是很有意思的，因为它对于噪声模型也没有任何约束。而实验中，关于噪声的部分说明了这个方法的有效性。但是如何从理论上来分析，我感觉有点困难。



## 方法

我们直接从这篇文章最后使用的方法讲起，而忽略作者其他的分析（我认为这些不重要）。

假设现在有一个去噪模型 $F_{\theta}(y)$，$y$ 是噪声图像，那么我们可以构造出另外一个噪声图像，噪声取决于当前的去噪模型，而不是真实的 $y$ 的噪声。
$$
\begin{aligned}
y_{2} & \equiv D\left(F_{\theta}(y)\right) \\
&=F_{\theta}(y)+n_{2} \\
&=F_{\theta}(y)+m \odot \hat{n} \\
&=F_{\theta}(y)+m \odot\left(y-F_{\theta}(y)\right)
\end{aligned}
$$
这里的 $m$ 是一个 -1 和 1 随机抽样的 maks 矩阵，用于构造 $y_2$ 的噪声。当 $y_2$ 构造出来之后，优化的目标是：
$$
\theta^{*}=\arg \min _{\theta}\left\|F_{\theta}\left(y_{2}\right)-\operatorname{Stopgrad}\left(F_{\theta}(y)\right)\right\|_{2}^{2}
$$
这里的 stopgrad 的含义是仅仅把 $F_{\theta}(y)$ 当作是 target，梯度计算仅仅是关于 $y_2$ 的部分。这个过程也可以从图示来理解：

![屏幕快照 2021-12-15 上午11.00.35](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-12-15 上午11.00.35.png)

当然，每次迭代训练的时候，$y_2$ 的 mask 矩阵都不一样。

作者的分析是这样的，上述损失函数可以分解成：
$$
\begin{gathered}
\eta_{y}^{(t)} \leftarrow F_{\theta^{(t-1)}}(y) \\
\theta^{(t)} \leftarrow \arg \min _{\theta}\left\|F_{\theta}\left(y_{2}\right)-\eta_{y}^{(t)}\right\|_{2}^{2}
\end{gathered}
$$
两个步骤。第一个步骤是计算出当前估计的后验均值。第二个步骤则是根据这个后验均值构造出一个噪声分布 $y_2$，然后再计算这个新的噪声分布的后验均值。如此循环往复，作者希望能够收敛到合适的结果。很显然，如果 $F_{\theta}$ 是一个非常好的去噪模型，确实可以得到真实的后验分布，那么很大程度上 $y_2$ 的分布和噪声图像分布 $y$ 是差不多的，那么这个不动点的结论成立。当然这个不动点不可能严格是后验分布，两者还是有一定差异。但是如果，实验的时候发现构造 $y_2$ 的噪声和 $y$ 的噪声非常接近的时候，就证明这个过程几乎收敛了。

作者的实验的确验证了这一点：

![屏幕快照 2021-12-15 下午1.38.22](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-12-15 下午1.38.22.png)

这个图表示了模拟的噪声的确越来越接近真实的噪声。下面这个图则显示了整个去噪模型的训练过程。

![屏幕快照 2021-12-15 下午1.38.29](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-12-15 下午1.38.29.png)

这篇文章的实验细节并没有太多需要叙述的：

1. U-Net 结构的网络
2. 128x128 patch 训练
3. 尝试了不同的噪声

效果上，稍逊于 Noise2Noise 和 U-Net，但是比其他的方法都要好。