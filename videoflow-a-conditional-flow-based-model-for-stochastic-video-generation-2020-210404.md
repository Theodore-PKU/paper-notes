# Video Flow: A Conditional Flow-based Model for Stochastic Video Generation

[TOC]

## 信息

Authors:

Year: ICLR 2020

Bibtex:

```
@article{kumar2019videoflow,
  title={Videoflow: A conditional flow-based model for stochastic video generation},
  author={Kumar, Manoj and Babaeizadeh, Mohammad and Erhan, Dumitru and Finn, Chelsea and Levine, Sergey and Dinh, Laurent and Kingma, Durk},
  journal={arXiv preprint arXiv:1903.01434},
  year={2019}
}
```

cite: 25

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/videoflow-a-conditional-flow-based-model-for-stochastic-video-generation.pdf)



## 概括

这篇文章研究的是视频生成（也就是一帧一帧的图像），使用了 flow 模型。方法概括起来很简单，首先用 flow 模型（这篇文章的基本模型是 glow）表示图像，然后得到不同 level 的隐变量。然后用一个自回归模型来表示不同帧（时间t）图像的隐变量之间的关系，用此前的帧的隐变量（同一个 level）和当前帧的隐变量（上一个level）用一个回归模型推测出一个新的隐变量（当前帧当前 level），在得到隐变量之后，就可以用 glow 模型生成对应帧的图像。隐变量在这里的表示都是高斯分布，所以回归模型的输出也是一个高斯分布（这样才可以sample，才会出现变化和多样性）。

这篇文章由于主要考虑的是视频生成，关于图像的部分，并没有太多创新，所以没有仔细看，仅仅了解主要的做法和思路（训练的方法，我没有仔细看，似乎文章关于这一点也提的不多）。这篇文章的启示在于隐变量在图像问题中是一个很重要的概念，生成模型其实是作为一种图像的表示，要根据问题来设计隐变量之间的变化。



## 方法

在这个小节，给出一些图示和公式，对文章的思路做进一步说明。

![屏幕快照 2021-04-06 上午8.52.31](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-06 上午8.52.31.png)

这是 glow 模型的基本框架，从文章中的细节来看，基本上没有什么改变。

![屏幕快照 2021-04-06 上午8.52.36](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-06 上午8.52.36.png)

这是隐变量之间的关系。看起来有一些复杂。这里主要是要描述 $z_{<t}^{(i)}, z_{t}^{(i)}, z_{t}^{(>i)}$ 之间的关系。

下面这个式子描述的是当前帧和此前所有帧之间的关系
$$
\begin{equation}
p_{\boldsymbol{\theta}}\left(\mathbf{z}_{t} \mid \mathbf{z}_{<t}\right)=\prod_{l=1}^{L} p_{\boldsymbol{\theta}}\left(\mathbf{z}_{t}^{(l)} \mid \mathbf{z}_{<t}^{(l)}, \mathbf{z}_{t}^{(>l)}\right)
\end{equation}
$$
下面这个式子描述的是具体的计算方式：
$$
\begin{equation}
\begin{aligned}
p_{\boldsymbol{\theta}}\left(\mathbf{z}_{t}^{(l)} \mid \mathbf{z}_{<t}^{(l)}, \mathbf{z}_{t}^{(>l)}\right) &=\mathcal{N}\left(\mathbf{z}_{t}^{(l)} ; \boldsymbol{\mu}, \sigma\right) \\
& \text { where }(\boldsymbol{\mu}, \log \sigma)=N N_{\boldsymbol{\theta}}\left(\mathbf{z}_{<t}^{(l)}, \mathbf{z}_{t}^{(>l)}\right)
\end{aligned}
\end{equation}
$$
这里的 $NN_{\theta}$ 就是一个 3D 残差网络。

