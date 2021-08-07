# Denoising Score-Matching for Uncertainty Quantiﬁcation in Inverse Problems

[TOC]

## 信息

Authors: Zaccharie Ramzi, Benjamin Remy, François Lanusse, Jean-Luc Starck, Philippe Ciuciu

Year: NeurIPS 2020 Workshop on Deep Learning and Inverse Problems

Bibtex:

```latex
@article{ramzi2020denoising,
  title={Denoising Score-Matching for Uncertainty Quantification in Inverse Problems},
  author={Ramzi, Zaccharie and R{\'e}my, Benjamin and Lanusse, Francois and Starck, Jean-Luc and Ciuciu, Philippe},
  journal={arXiv preprint arXiv:2011.08698},
  year={2020}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/denoising-score-matching-for-uncertainty-quantiﬁcation-in-inverse-problems.pdf)



## 概括





## 方法

作者的目标函数是后验概率 $p(\boldsymbol{x} \mid \boldsymbol{y}) \propto p(\boldsymbol{y} \mid \boldsymbol{x}) p(\boldsymbol{x}) $，所以考虑的不确定性就是重建本身的分布的不确定性。这篇文章采用的方法是基于 $\nabla_x \log p(x)$ 的采样方法，比如 variational inference (Matthew D. Hoffman, David M. Blei, Chong Wang, and John Paisley. Stochastic variational inference. Journal of Machine Learning Research, 14:1303–1347, 2013. ISSN 15324435.)，朗之万扩散，HMC (Radford M. Neal. MCMC using hamiltonian dynamics. Handbook of Markov Chain Monte Carlo, pages 113–162, 2011. doi: 10.1201/b10905-6.) 等。我感觉和之前的一篇用 VAE 的不确定性估计的文章有些相似。

Metropolis Hastings-calibrated HMC 的方法在附录 B 中有详细的说明。

后验概率有两个部分，p(y|x) 的 score 可以解析地推出来，但是 prior 的部分需要用网络来训练（应该就是这个部分用到了 score-matching）

### Deep Denoising Score Matching

核心想法是，一个最优的去燥自编码器满足
$$
\boldsymbol{r}^{\star}\left(\boldsymbol{x}^{\prime}, \sigma\right)=\boldsymbol{x}^{\prime}+\sigma^{2} \nabla_{\boldsymbol{x}} \log p_{\sigma^{2}}\left(\boldsymbol{x}^{\prime}\right)
$$
这里的 $x'$ 是带高斯噪声的信号，$p_{\sigma^2}$ 则是噪声图像的分布。（这个和之前看的文章联系起来了）

当噪声很小的时候，denoiser 就可以看成是 p(x) 的 score。

为了训练 denoiser，作者采用的方法参考了别人的损失函数：
$$
\mathcal{L}_{D S M}=\underset{\boldsymbol{x} \sim P}{\mathbb{E}} \underset{\boldsymbol{u} \sim \mathcal{N}(0, I) \atop \sigma_{s} \sim \mathcal{N}\left(0, s^{2}\right)}{\mathbb{E}}\left\|\boldsymbol{u}+\sigma_{s} \boldsymbol{r}_{\theta}\left(\boldsymbol{x}+\sigma_{s} \boldsymbol{u}, \sigma_{s}\right)\right\|_{2}^{2}
$$
训练出来的结果就是网络 $r_{\theta}(x, \sigma)$ 是 $\nabla_{\boldsymbol{x}} \log p_{\sigma^{2}}(x)$

### Annealed Hamiltonian-Monte Carlo Sampling

采样过程和 song yang 的方法非常接近。

首先考虑一个带噪声版本的后验概率（也就是说 x 是带噪声的）
$$
\log p_{\sigma^{2}}(\boldsymbol{x} \mid \boldsymbol{y})=\log p_{\sigma^{2}}(\boldsymbol{y} \mid \boldsymbol{x})+\log p_{\sigma^{2}}(\boldsymbol{x})+c s t
$$
其中：$\log p_{\sigma^{2}}(\boldsymbol{y} \mid \boldsymbol{x})=-\frac{\|\boldsymbol{x}-f(\boldsymbol{x})\|_{2}^{2}}{2\left(\sigma_{n}^{2}+\sigma^{2}\right)}+c s t$

这个是很好推的。注意，这里的 $\sigma_n$ 是测量的误差，也就是 forward 过程的噪声。

作者提出的采样方法就是逐渐减小温度（sigma）使得采样逐渐逼近真实分布。也就是说，将 $\log p_{\sigma^{2}}(\boldsymbol{x} \mid \boldsymbol{y})$ 作为采样的核心。不过具体的采样过程呢？



## 实验

作者的实验是 MRI 重建，考虑的是单线圈的 fastMRI knee 的任务。比较对象是 UPDNet，作者说是目前的 sota（好像就是作者自己提出的）。加速程度为 4.  $\sigma_n$ 设为 0.1

网络结构：U-Net + residual blocks，使用了谱范数正则项（每个卷积层的参数），谱范数设为 2 左右。详细参见附录 A。（The network is a 3-scale U-net with residual blocks composed of 3 convolutions followed by a batch normalisation. ）

噪声水平作为网络输入的做法是：For input noise level conditioning, we concatenate a noise standard deviation map to the input and in the lower scale of the network.

we also divide the output of the network by the absolute value of the noise power.

ADAM training，学习率是 1e-4。image scaled by 1e6.  $\sigma_s$ 的采样是从 N(0, 50) 得到的。

使用 HMC 过程采样后，还有一步的去噪声。方法参考了 Alexia Jolicoeur-Martineau, Rémi Piché-Taillefer, Rémi Tachet des Combes, and Ioannis Mitliagkas. Adversarial score matching and improved sampling for image generation. pages 1–29, 2020. URL http://arxiv.org/abs/2009.05475.

### HMC 过程

采样过程是：
$$
\begin{aligned}
&\boldsymbol{m}_{t+\frac{\alpha}{2}}=\boldsymbol{m}_{t}+\frac{\alpha}{2} \nabla_{\boldsymbol{x}} \log p\left(\boldsymbol{x}_{t} \mid \boldsymbol{y}\right) \\
&\boldsymbol{x}_{t+\alpha}=\boldsymbol{x}_{t}+\alpha \mathbf{M}_{\boldsymbol{C}}^{-1} \boldsymbol{m}_{t+\frac{\alpha}{2}} \\
&\boldsymbol{m}_{t+\alpha} \quad=\boldsymbol{m}_{t+\frac{\alpha}{2}}+\frac{\alpha}{2} \nabla_{\boldsymbol{x}} \log p\left(\boldsymbol{x}_{t+\alpha} \mid \boldsymbol{y}\right)
\end{aligned}
$$
这里的 alpha 是 step size，m 是一个 momentum，M 是preconditioning matrix，但实际上是 idnetity matrix。梯度计算用了 $\nabla_{\boldsymbol{x}} \log p\left(\boldsymbol{x}_{t} \mid \boldsymbol{y}\right)=\nabla_{\boldsymbol{x}} \log p\left(\boldsymbol{y} \mid \boldsymbol{x}_{t}\right)+\nabla_{\boldsymbol{x}} \log p\left(\boldsymbol{y} \mid \boldsymbol{x}_{t}\right)$。

HMC 需要计算接受概率，$\min \left\{1, p\left(\boldsymbol{x}^{*}\right) q\left(\boldsymbol{x}_{n} \mid \boldsymbol{x}^{*}\right) / p\left(\boldsymbol{x}_{n}\right) q\left(\boldsymbol{x}^{*} \mid \boldsymbol{x}_{n}\right)\right.$，但是 q 和 p 我不知道怎么算。p 在这篇文章通过下面的积分来计算：
$$
\log p\left(\boldsymbol{x}_{*}\right)-\log p\left(\boldsymbol{x}_{n}\right)=\int_{0}^{1} \nabla_{\boldsymbol{x}} \log p\left(t *\left(\boldsymbol{x}_{*}-\boldsymbol{x}_{n}\right)+\boldsymbol{x}_{n}\right) \cdot\left(\boldsymbol{x}_{*}-\boldsymbol{x}_{n}\right) d t
$$
这个数值计算其实很麻烦。另外 q 我也不知道怎么算。这里的 x* 是当前的更新，xn 是上一个被接受的点。

我的问题是：HMC 用了接受概率，但是 song yang 的文章里好像没有这个。那么接受概率是否是重要的呢？以及有什么办法可以规避这个呢？因为我觉得这个很麻烦。

出发点是 zf 重建结果，并且加上了高斯噪声。noise 是 100. 这篇文章似乎把输入的值都调得很大？不知道是为什么。

作者表示，由于温度最后没有下降地非常低，所以必须要用一个去噪声步骤。如何选择最优的 HMC 过程以及温度都是很困难的，也许这就是 chance。

实验的结果可以看出确实生成的图像是有变化的，我认为这一点非常好，而且这不像 VAE 之类的模型，有什么 variance 控制，确实是按照理论来实现了分布的采样，缺点主要是有一个去噪声，这一点是硬伤。

和 UPDNet 比较的结果，可以发现，PSNR 这样的评价指标是比不上的，每个 sample 的 PSNR 其实都挺差的，平均以后也还是不太行，但是给出了分布。



## 参考文献

这个部分基本包含了 related work 小节的内容。

Vanessa Böhm, François Lanusse, and Uroš Seljak. Uncertainty Quantiﬁcation with Generative Models. (NeurIPS):1–9, 10 2019. URL http://arxiv.org/abs/1910.10046.

Ga Wu, Justin Domke, and Scott Sanner. Conditional Inference in Pre-trained Variational Autoencoders via Cross-coding. 5 2018. URL http://arxiv.org/abs/1805.07785.

这两篇文章作者说是利用训练好的生成模型的隐变量空间的变分推断方法。有点不太懂。

Jonas Adler and Ozan Öktem. Deep Bayesian Inversion. 11 2018. URL http://arxiv.org/abs/ 1811.05910. 这篇是 WGAN 方法的不确定性。

Alexander Denker, Maximilian Schmidt, Johannes Leuschner, Peter Maass, and Jens Behrmann.

Conditional Normalizing Flows for Low-Dose Computed Tomography Image Reconstruction. 6 2020. URL http://arxiv.org/abs/2006.06270. 这篇文章用 flow 模型算 CT 的重建。

Francesco Tonolini, Jack Radford, Alex Turpin, Daniele Faccio, and Roderick Murray-Smith.

Variational Inference for Computational Imaging Inverse Problems. 21:1–46, 4 2019. URL http://arxiv.org/abs/1904.06264. 这篇文章用的是 VAE

这三篇文章则非常好懂，直接利用生成网络来 sample 不同的样本。

Matthew D. Hoffman, David M. Blei, Chong Wang, and John Paisley. Stochastic variational inference. Journal of Machine Learning Research, 14:1303–1347, 2013. ISSN 15324435.

Radford M. Neal. MCMC using hamiltonian dynamics. Handbook of Markov Chain Monte Carlo, pages 113–162, 2011. doi: 10.1201/b10905-6.

这两篇文章是使用 score 的采样方法。

Jae Hyun Lim, Aaron Courville, Christopher Pal, and Chin-Wei Huang. AR-DAE: Towards Unbiased Neural Entropy Gradient Estimation. 2020. URL http://arxiv.org/abs/2006.05164. 这篇文章是作者采用的估计 score 的方法。我感觉和 song yang 他们的方法差不多啊。

关于 MRI 重建，提到了这篇文章

Nicola Pezzotti, Sahar Youseﬁ, Mohamed S Elmahdy, Jeroen Van Gemert, Christophe Schülke, Mariya Doneva, Tim Nielsen, Sergey Kastryulin, Boudewijn P F Lelieveldt, Matthias J P Van Osch, Elwin De Weerdt, and Marius Staring. An Adaptive Intelligence Algorithm for Undersampled Knee MRI Reconstruction: Application to the 2019 fastMRI Challenge. Technical report, 2020.

Zaccharie Ramzi, Philippe Ciuciu, and Jean Luc Starck. Benchmarking MRI reconstruction neural networks on large public datasets. Applied Sciences (Switzerland), 10(5), 2020. ISSN 20763417. doi: 10.3390/app10051816. 这篇文章我们是不是看过了？

 Alexia Jolicoeur-Martineau, Rémi Piché-Taillefer, Rémi Tachet des Combes, and Ioannis Mitliagkas. Adversarial score matching and improved sampling for image generation. pages 1–29, 2020. URL http://arxiv.org/abs/2009.05475. 这篇文章是最后的去噪声步。

Yang Song and Stefano Ermon. Improved Techniques for Training Score-Based Generative Models. URL http://arxiv.org/abs/2006.09011. 这篇文章也要看。

Yuichi Yoshida and Takeru Miyato. Spectral Norm Regularization for Improving the Generalizability of Deep Learning. 谱范数参考的是这篇文章。但好像和 NVAE 的不一样。