# Noise Distribution Adaptive Self-Supervised Image Denoising using Tweedie Distribution and Score Matching

[TOC]

## 信息

作者：Kwanyoung Kim, Taesung Kwon, Jong Chul Ye

年份：2021

期刊会议：arXiv

Bibtex:

```latex
@article{kim2021noise,
  title={Noise Distribution Adaptive Self-Supervised Image Denoising using Tweedie Distribution and Score Matching},
  author={Kim, Kwanyoung and Kwon, Taesung and Ye, Jong Chul},
  journal={arXiv preprint arXiv:2112.03696},
  year={2021}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/noise-distribution-adaptive-self-supervised-image-denoising-using-tweedie-distribution-and-score-matching.pdf)



## 概括

详读。

这篇文章研究的是自监督去噪问题，是 Noise2Score 这篇文章的改进版本。Noise2Score 的方法如果知道了噪声类型和噪声水平，效果应该是非常好的。但是如果不知道 noise level，就只能用图像质量函数来决定最后应该用哪一个噪声水平。这是一个很大的缺陷。这篇文章提出的方法则重点解决的是如何估计噪声类型和噪声水平。作者应用了 Tweedie's distribution 的形式，通过所谓的 saddle point approximation 的方法来估计出噪声类型，然后再估计出噪声水平，最后用 Noise2Score 的方法进行去噪。因此从方法的流程上，比 Noise2Score 多了两步，其他都是一样的。估计方法就是利用 saddle point approximation，选择 y 图像的一个小扰动，然后根据假设计算出参数的估计，由此得到分布类型和噪声水平的估计。具体看后面的小节。通过分析，我们发现，这个 saddle point approximation 方法就是假设了梯度为 0，这个假设并不准确，但实际上通过 median 这样的操作，会非常有效。具体的分析在后面的小节进行说明。这篇文章的方法仍然局限在 noise level 在所有的像素是均匀的基础上，我们猜测，如果不是均匀的噪声水平，这个方法应该会失效。但不管怎么说，这篇文章在 noise type 的估计上，还是有实际价值的，是可以借鉴的。



## 方法

![屏幕快照 2021-12-15 下午4.43.22](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-12-15 下午4.43.22.png)

我们仅仅介绍怎么估计噪声类型和噪声水平，并进行一些分析。

我们先说明什么是 Tweedie's distribution. 简单来说，就是指数族分布可以用一个统一的形式来表示，此时增加了新的参数来表示不同的噪声类型。
$$
p(y ; \mu, \phi)=b_{\rho}(y, \phi) \exp \left(\frac{-d(y, \mu)}{2 \phi}\right) \\
d(y, \mu)=2\left(\frac{y^{2-\rho}}{(1-\rho)(2-\rho)}-\frac{y \mu^{1-\rho}}{1-\rho}+\frac{\mu^{2-\rho}}{2-\rho}\right), \text{for } \rho \neq 0, 1, 2
$$
$\mu$ 是均值。$\phi$ 是一个和噪声水平有关的参数。$\rho$ 是一个和噪声类型有关的参数，这里的 $b_p$ 函数通常没有解析形式。但是有一种比较简单的替代形式：
$$
p(y ; \mu, \phi)=\left(2 \pi \phi y^{\rho}\right)^{-\frac{1}{2}} \exp \left(\frac{-d(y, \mu)}{2 \phi}\right)
$$
这些都是别人提出的。我也不知道对不对，是否准确。总之，现在作者是基于 (5) 式来表示各种不同的噪声模型。

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-12-15 下午5.03.01.png" alt="屏幕快照 2021-12-15 下午5.03.01" style="zoom:50%;" />

作者还给了一个表，这是常见的一些噪声。

基于 Tweedie's distribution，作者推出了后验分布的表达式：
$$
\hat{\mu}=\mathbb{E}[\mu \mid y]=y(1+(1-\rho) \alpha(y, \rho, \phi))^{\frac{1}{1-\rho}} \\
\alpha(y, \rho, \phi)=\phi y^{\rho-1}\left(\frac{\rho}{2 y}+l^{\prime}(y)\right)
$$
并且给出了不同噪声类型的结果（这个忽略）。

怎么估计噪声类型呢？作者的想法非常简单，如果 $y_1$ 和 $y_2$ 的差别非常小（实际上通过增加一个非常小的高斯噪声完成），那么两者的后验分布的均值也应该差别非常小 $\mathbb{E}\left[\mu \mid y_{1}\right] \simeq \mathbb{E}\left[\mu \mid y_{2}\right]$。所以作者直接假设了
$$
\alpha\left(y_{1}, \rho, \phi\right) \simeq \alpha\left(y_{2}, \rho, \phi\right)
$$
实际上就是当作等号。并由这个式子推出了命题 3，即 $\rho$ 的估计，结果就不放出来了。这里 $\phi$ 是不影响的，被消去了。根据估计的 $\rho$ 来判断是哪一个噪声水平。当然作者的做法非常粗糙：

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-12-15 下午5.11.25.png" alt="屏幕快照 2021-12-15 下午5.11.25" style="zoom:50%;" />

下面是噪声水平的估计方法。

假设噪声类型已知。用同样的方法，由于可以直接用 Tweedie's formula，对于这些噪声，可以算出 $y_1, y_2$ 的后验分布均值，作者直接令两者相等，求解 $\phi$. 求解完毕之后，整个噪声模型就确定了。至此，整个方法论述完毕。

不过这里的论述都是基于一维变量的，如果是高维变量，只要当成是 pixel-wise 来计算就可以了。这个影响不大。

> 我们接下来进行一些分析。的确后验分布的均值差异非常小，但是为什么可以直接假设 $\alpha\left(y_{1}, \rho, \phi\right) = \alpha\left(y_{2}, \rho, \phi\right)$ 以及 $\mathbb{E}\left[\mu \mid y_{1}\right] = \mathbb{E}\left[\mu \mid y_{2}\right]$ 呢？这个假设的背后是什么？就是梯度为 0 ！但是这个假设显然是不对的 ！因为我们没有任何理论认为这是成立的。

但是实验的结果为什么好呢？甚至作者说噪声水平的估计非常准确呢？对于噪声类型，因为有很多图像，如果实际的梯度不是 0，那么有正有负，可能会得到很多不同的解，这些解也许刚好就在正确值的附近，那么取平均或者是 median 得到的结果是相对正确的。特别是整个数据集都是相同的噪声类型的情况下。因此估计准确。对于噪声的水平，也是这样的。作者在附录中明确地写明了，$\phi$ 的估计用的是 median，那么 median 的结果就很可能是准确的。这个可以理解为，当梯度不是 0 的时候，才能计算真正的解，当梯度当成 0 算的时候，有误差。但是这个误差会因为 median 而消掉。

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-12-15 下午5.24.31.png" alt="屏幕快照 2021-12-15 下午5.24.31" style="zoom:50%;" />

这个图是 $\rho$ 的估计，也就是噪声类型。我觉得是一个典型的例子。作者没有给 $\phi$ 的图，我认为非常不合理。我个人的猜测是如果不考虑 median，也画出类似的图，应该是一个分布，也就是说其实估计地并不准。**这个应该是可以做实验验证的。之后做自监督的实验的时候验证一下。**



## 实验

实验部分，我们只是说明作者是怎么设计实验的。

作者要验证这篇文章提出的方法有效，所以还是设计了三种不同类型的噪声以及不同的噪声水平。但是噪声水平当作是模型未知的。对于其他的 Noise2X 的方法而言，这并不重要，因为它们本身就是 blind model。对于 Noise2Score，噪声水平估计的准确与否非常重要。这里我们似乎没有提到噪声类型的估计，实际上，作者只有在 CT 实验中真正应用了这个方法。其他模型其实可以看成是默认知道，或者已经估计好了。CT 的实验我们稍后再说。作者发现 Noise2Score 的噪声水平的估计比这篇文章提出的方法要稍差一点。所以最后效果会稍微差一点。

CT 的实验，作者用 50% dose 的图像作为noise 数据，估计噪声类型的时候发现可以看成是高斯的。但是最后模型的表现并没有另外一个自监督的方法好，我认为这是因为 CT 数据的高斯噪声是不均匀的，所以用作者提出的方法，估计出来的高斯噪声水平就不是准确的了，因此效果就差了。

另外，关于 score function 的估计，这次作者加了两个改进，一个是训练的时候 $\sigma_a$ 用的是 geometric sequence，一个是模型参数 moving average。其他没有变化。作者的消融实验说明这些改进是有效的。使得 score function 的估计更好了。