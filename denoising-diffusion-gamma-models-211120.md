# Denosing Diffusion Gamma Models

[TOC]

## 信息

作者：Eliya Nachmani, Robin San Roman, Lior Wolf

期刊会议: arXiv

年份: 2021

Bibtex:

```latex
@article{nachmani2021denoising,
  title={Denoising Diffusion Gamma Models},
  author={Nachmani, Eliya and Roman, Robin San and Wolf, Lior},
  journal={arXiv preprint arXiv:2110.05948},
  year={2021}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/denoising-diffusion-gamma-models.pdf)



## 概括

通读。

这篇文章的切入点在于扩散过程中的噪声类型，通常选择的是高斯噪声，因为它有很好的性质。作者认为，高斯噪声在 ddpm 中的核心性质是两个独立的高斯噪声的和仍然是高斯噪声。作者认为 Gamma 分布也满足这个性质，因此可以设计一个基于 Gamma 噪声的扩散过程。类似于 ddpm 的推导，作者也推出了损失函数等内容。但是由于 Gamma 分布没有好的解析形式，非常难计算。于是作者采用了一些放缩的技巧。将 variational lower bound 进一步放缩成一个新的上界，最后推出了一个 1 范数的损失函数。作者也给出了相应的采样算法。作者把这个方法称为 ddgm，并用在图像数据集上进行了实验。

我认为作者思考的角度是合理的，但是实际的做法并没有看出比基于高斯噪声的 ddpm 高明，甚至我认为要差很多。这个思路不适合进一步跟进。



## 方法

在这个小节，我们简单地将 ddgm 的推导过程进行梳理。

![屏幕快照 2021-11-20 下午4.22.50](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-11-20 下午4.22.50.png)

这个定义的形式和 ddpm 几乎一样。由于 Gamma 分布的均值不为 0，所以在形式上作者去掉了 Gamma 分布的均值。Gamma 分布有两个参数，$k_t, \theta_t$，这两个参数用于控制方差。作者的设定使得方差（也就是 noise level schedule）和 ddpm 保持一致。

给定扩散过程之后，就可以推导出 $x_t$ 关于 $x_0$ 的表达式以及其他重要的后验分布的表达式。
$$
x_{t}=\sqrt{\bar{\alpha}_{t}} x_{0}+\left(\bar{g}_{t}-\bar{k}_{t} \theta_{t}\right)
$$
其中，$\bar{g}_{t} \sim \Gamma\left(\bar{k}_{t}, \theta_{t}\right) \text { and } \bar{k}_{t}=\sum_{i=1}^{t} k_{i}$.

作者经过非常复杂的推导，得到了：
$$
\begin{aligned}
q\left(x_{t-1} \mid x_{0}, x_{t}\right) &=\frac{\left(\left(x_{t}-\sqrt{1-\beta_{t}} x_{t-1}+k_{t} \theta_{t}\right)^{k_{t}-1} e^{-\left(x_{t}-\sqrt{1-\beta_{t}} x_{t-1}+k_{t} \theta_{t}\right) / \theta_{t}}\right)}{\Gamma\left(k_{t}\right) \theta_{t}} \\
& \cdot \frac{\left(\left(x_{t-1}-\sqrt{\bar{\alpha}_{t-1}} x_{0}+\bar{k}_{t-1} \theta_{t-1}\right)^{\bar{k}_{t-1}-1} e^{-\left(x_{t}-\sqrt{\bar{\alpha}_{t-1}} x_{0}+\bar{k}_{t-1} \theta_{t-1}\right) / \theta_{t-1}}\right)}{\Gamma\left(\bar{k}_{t-1}\right) \theta_{t}^{\bar{k}_{t-1}}} \\
& \cdot \frac{\Gamma\left(\bar{k}_{t}\right) \theta_{t}^{\bar{k}_{t}}}{\left(\left(x_{t}-\sqrt{\bar{\alpha}_{t}} x_{0}+\bar{k}_{t} \theta_{t}\right)^{\bar{k}_{t}-1} e^{-\left(x_{t}-\sqrt{\bar{\alpha}_{t}} x_{0}+\bar{k}_{t} \theta_{t}\right) / \theta_{t}}\right)}
\end{aligned}
$$
非常复杂的一个计算。

关于损失函数，首先推导出 vlb 损失函数：
$$
E\left[-\log \left(p_{\theta}\left(x_{0}\right)\right] \leq E_{q}\left[-\log p\left(x_{T}\right)-\sum_{t \geq 1} \log \frac{p_{\theta}\left(x_{t-1} \mid x_{t}\right)}{q\left(x_{t} \mid x_{t-1}\right)}\right]=L_{V L B}\right.
$$
这里的核心计算是两个分布的 KL 散度。
$$
L_{t-1}=D_{K L}\left(q\left(x_{t-1} \mid x_{0}, x_{t}\right) \| q\left(x_{t-1} \mid \hat{x}_{0}, x_{t}\right)\right) \\
\hat{x}_{0}=\frac{x_{t}-\sqrt{1-\bar{\alpha}_{t}} \varepsilon_{\theta}\left(x_{t}, t\right)}{\sqrt{\bar{\alpha}_{t}}}
$$
这里的 $q\left(x_{t-1} \mid \hat{x}_{0}, x_{t}\right)$ 实际上就是 $p_{\theta}\left(x_{t-1} \mid x_{t}\right)$. 但是这个计算是算不出来的，所以作者用了很多放缩的技巧得到一个结论：
$$
L_{t-1} \leq E_{q\left(x_{t-1} \mid x_{0}, x_{t}\right)}\left(C_{1}+C_{2}+\frac{C_{3}}{\bar{g}_{t}}+\frac{C_{4}}{\bar{g}_{t-1}}\right)\left|x_{0}-\hat{x}_{0}\right|=\left(C_{1}+C_{2}+\frac{C_{3}}{\bar{g}_{t}}+\frac{C_{4}}{\bar{g}_{t-1}}\right)\left|x_{0}-\hat{x}_{0}\right|
$$
所以就直接采用 1 范数的形式。最后得到的损失函数是：
$$
\mathcal{L}=\left|\frac{\bar{g}_{t}-\bar{k}_{t} \theta_{t}}{\sqrt{1-\bar{\alpha}_{t}}}-\varepsilon_{\theta}\left(x_{t}, t\right)\right|
$$
注意，这里的 $\bar{g}_{t}$ 是 Gamma 分布的采样。

训练和采样算法基本上和 DDPM 是一样的，只不过替换了高斯噪声的部分。

![屏幕快照 2021-11-20 下午4.36.56](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-11-20 下午4.36.56.png)

实验的结果显示，这种方法在迭代步数比较小时确实不差，不过迭代步数增加后表现没那么好。不过我并不打算考虑这种方法。实验数据大致如下：

![屏幕快照 2021-11-20 下午4.39.32](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-11-20 下午4.39.32.png)