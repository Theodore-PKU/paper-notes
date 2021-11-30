# Conditional Image Generation with Score-Based Diffusion Models

[TOC]

## 信息

作者：Georgios Batzolis, Jan Stanczuk, Carola-Bibiane Schönlieb, Christian Etmann

年份：2021

期刊会议：arXiv

Bibtex：暂无

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/conditional-image-generation-with-score-based-diffusion-models.pdf)

有参考论文需要看，这两篇都挺重要的。



## 概括

通读。

这篇文章考虑的是 score-based model 在有条件生成时的如何选择模型框架的问题。这里的框架指的是如何训练网络。通常两种做法，一种是将条件作为网络输入之一重新训练一个模型，一种是先训练一个无条件生成模型，再把条件考虑进来。这篇文章讨论的是第一种方法，但是具体而言，条件是如何应用的，其实并没有定论。最简单的方法应该是类似于 SR3 的那种。此外作者还讨论了另外两种方法，这两种方法都会对条件 y 进行 diffuse，因此略有不同。其中一种是此前别人提出来的，y 的 diffuse 过程和 x 保持一致，第二种则是作者提出来的，所谓 y 和 x 是独立的 diffuse 过程。作者表示后两种方法其实是牺牲了 approximation，但是减小了 optimization error。但是我感觉这种方法在形式上并不优美，我认为不是问题的核心。只能说是一种尝试，在实验结果上有一些区别。具体的数学形式表述，参考方法的部分。



## 方法

在 score-based 模型中，核心是要得到 score function，也就是要能够估计 $\nabla_{x_{t}} \ln p_{t}(x_{t})$. 对于有条件生成，则是要估计 $\nabla_{x_{t}} \ln p_{t}(x_{t} | y)$ 方法。这里 $x_{t}$ 的扩散过程是和 $y$ 无关的。有两种思路，第一种就是直接估计，另一种则是用贝叶斯公式：
$$
\nabla_{x_{t}} \ln p\left(x_{t} \mid y\right)=\nabla_{x_{t}} \ln p\left(x_{t}\right)+\nabla_{x_{t}} \ln p\left(y \mid x_{t}\right)
$$
第二种方法其实也有不少实践，只不过理论的讨论比较少。

第一种方法的通用做法其实是：
$$
\frac{1}{2} \mathbb{E}_{t \sim U(0, T)  x_{0}, y \sim p\left(x_{0}, y\right)  x_{t} \sim p\left(x_{t} \mid x_{0}\right)}{\left[\lambda(t)\left\|\nabla_{x_{t}} \ln p\left(x_{t} \mid x_{0}\right)-s_{\theta}\left(x_{t}, y, t\right)\right\|_{2}^{2}\right]}
$$
作者证明了这个做法的理论正确性（和我们之前的分析完全一致，因为 $x_{t}$ 的扩散过程和 y 无关。

还有一种做法则是估计 $\nabla_{x_{t}} \ln p_{t}(x_{t} | y_{t})$，用这个来近似 $\nabla_{x_{t}} \ln p_{t}(x_{t} | y)$. 因此损失函数就变了：
$$
\frac{1}{2} \underset{z_{0} \sim p_{0}\left(z_{0}\right) \atop z_{t} \sim p\left(z_{t} \mid z_{0}\right)}{\mathbb{E}_{t \sim U(0, T)}}\left[\lambda(t)\left\|\nabla_{z_{t}} \ln p\left(z_{t} \mid z_{0}\right)-s_{\theta}\left(z_{t}, t\right)\right\|_{2}^{2}\right]
$$
这里的 $z_{t} = (x_{t}, y_{t})$，不过这里通常 $y_{t}$ 的扩散过程和 $x_{t}$ 是有关的。在推断的时候，因为 $y$ 已知，所以会用 $\hat{y}_{t} \sim p\left(y_{t} \mid y\right)$ 来 sample $y_{t}$。作者表示，我们不一定要使用和 $x_{t}$ 一样的扩散过程，如果使用另一种扩散过程，就变成了这篇文章的 CMDE 方法。当然，使用这种方法后，作者说 $\lambda(t)$ 也需要变。有点点不太理解。

最后作者还考虑了所谓的 maximum likelihood 的权重（就是 Song Yang 等人提出的权重设计方法）。

总而言之，我觉得这种方法还不如第一种通用做法。作者说这是增加了 approxmation error（因为估计的对象变成 $\nabla_{x_{t}} \ln p_{t}(x_{t} | y_{t})$ 了，有误差），但是会使得训练变得更容易一点。但我还是不喜欢。之后是不会考虑这种做法的。就算是考虑这种做法，我也觉得 (3) 很奇怪，为什么一定要估计 $y_{t}$ 的噪声呢？我们仅仅估计 $x_{t}$ 的噪声不行吗？这样会让 code 变得简单，这种双重的在 mri 重建问题上会显得非常麻烦。

作者通过三个任务来比较不同的 score-based 条件生成的方法的效果差异。其中超分辨问题还和一个叫 HCFlow 的方法比较。这篇文章应该要看一下。另外，还有一个和时间有关的 score-based 的文章是需要看的。



## 参考论文

Yusuke Tashiro, Jiaming Song, Yang Song, and Stefano Ermon. Csdi: Conditional score-based diffusion models for probabilistic time series imputation, 2021. 这篇似乎和时间序列有关，也许会启发我们思考 PDE 的问题。

Jingyun Liang, Andreas Lugmayr, Kai Zhang, Martin Danelljan, Luc Van Gool, and Radu Timofte. Hierarchical conditional ﬂow: A uniﬁed framework for image superresolution and image rescaling, 2021. 这是一篇超分辨的 flow 文章，是 2021 cvpr 的文章，已经下载了，需要整理文献。