# SoftFlow: Probabilistic Framework for Normalizing Flow on Manifolds

[TOC]

## 信息

Authors: Hyeongju Kim, Hyeonseung Lee, Woo Hyun Kang, Joun Yeop Lee, Nam Soo Kim

Year: NeurIPS 2020

Bibtex:

```
@article{kim2020softflow,
  title={SoftFlow: Probabilistic Framework for Normalizing Flow on Manifolds},
  author={Kim, Hyeongju and Lee, Hyeonseung and Kang, Woo Hyun and Lee, Joun Yeop and Kim, Nam Soo},
  journal={Advances in Neural Information Processing Systems},
  volume={33},
  year={2020}
}
```

cite: 1



## 概括

这篇文章研究的是 flow 模型中 x 的实际维度和 z 不匹配的问题。一般来说，在 flow 模型中，为了解决可逆性，x 和 z 都是相同维度的。但是 x 的信号分布可能在只是在一个更低维的空间上。此时训练可能会出一些问题。我认为这个问题确实很关键。作者给了一个非常简单的 toy 实验，验证了这种维度不匹配带来的问题。

为了解决这个问题，作者提出的方法是对 x 加一个噪声 c，噪声的维度是高维的，同时 flow 模型变成一个条件 flow 模型，噪声是条件，x = f(z|c)。作者把这个做法推广到 flow 模型的一个变体方法上 FFJORD。

不过在这个训练方法下，模型在 sample 的时候需要指定 c，作者的做法是给一个很小的噪声（或者为 0）。作者没有给出 inference 的做法。我猜测的做法就是自己随机加一个 c？或者不加 c 直接当作 c 为 0 计算 z = f^-1(x|0).

另外作者在这篇文章还考虑了三维点云的问题，不过这不是我关心的内容就不看了。



## 方法

![屏幕快照 2021-03-30 下午1.24.18](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-30 下午1.24.18.png)

这是作者给出的实验，说明维度不匹配的问题是很严重的。

![屏幕快照 2021-03-30 下午1.24.49](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-30 下午1.24.49.png)

这个图给出了作者提出的训练方法。c 实际是一个噪声参数，实际的噪声是 sample 的结果。下式是应用这个做法的损失函数。
$$
\begin{equation}
\log p_{X}\left(x_{i}^{\prime} \mid c_{i}\right)=\log p_{Z}(z)-\log \left|\operatorname{det}\left(\frac{\partial f\left(z \mid c_{i}\right)}{\partial z}\right)\right|
\end{equation}
$$
sample 的时候是：
$$
\begin{equation}
\begin{array}{c}
z_{s p} \sim p_{Z}(z) \\
x_{s p}=f\left(z_{s p} \mid c_{s p}\right)
\end{array}
\end{equation}
$$
这里的 $c_{sp}$ 是指定的。

flow 模型有一种变体，是将 flow 模型中的 x 到 z 的变化过程变成一个时间演化过程（FFJORD采用了这种模型）：
$$
\begin{equation}
\begin{array}{c}
z\left(t_{0}\right)=z\left(t_{1}\right)+\int_{t_{1}}^{t_{0}} f(z(t), t) d t \\
\log p\left(z\left(t_{1}\right)\right)=\log p\left(z\left(t_{0}\right)\right)-\int_{t_{0}}^{t_{1}} \operatorname{Tr}\left(\frac{\partial f(z(t), t)}{\partial z(t)}\right) d t
\end{array}
\end{equation}
$$
这里的两个时间点分别表示 x 和 z。在作者的方法下，这种做法也相应的改动。
$$
\begin{equation}
\frac{d z(t)}{d t}=f\left(z(t), t, c_{i}\right), \quad z\left(t_{0}\right)=z, \quad z\left(t_{1}\right)=x_{i}+\nu_{i}
\end{equation}
$$
在实验中，作者基于 FFJORD 的 softflow 改进，验证了这种想法的效果。下图是实验结果：

![屏幕快照 2021-03-30 下午1.26.10](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-30 下午1.26.10.png)

可以看到，确实很有效。然而这个方法我认为对于图像的借鉴意义不大。不过作者提出的这个问题，我认为是非常重要的。



## 参考文献

Rezende, D. J., Papamakarios, G., Racanière, S., Albergo, M. S., Kanwar, G., Shanahan, P. E., and Cranmer, K. Normalizing ﬂows on tori and spheres. arXiv preprint arXiv:2002.02428, 2020.