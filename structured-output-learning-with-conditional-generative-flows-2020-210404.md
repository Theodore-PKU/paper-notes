# Structured Output Learning with Conditional Generative Flows

[TOC]

## 信息

Authors: You Lu, Bert Huang

Year: AAAI 2020

Bibtex:

```
@inproceedings{lu2020structured,
  title={Structured output learning with conditional generative flows},
  author={Lu, You and Huang, Bert},
  booktitle={Proceedings of the AAAI Conference on Artificial Intelligence},
  volume={34},
  number={04},
  pages={5005--5012},
  year={2020}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/structured-output-learning-with-conditional-generative-flows.pdf)

cite: 14



## 概括

这篇文章设计了一个 c-Glow 模型（conditional），生成结构化输出。这里的结构化输出是说输出可以是非图像的输出，比如离散的分割结果等。假设输出是 y，条件是 x，这里的条件基本上就是图像。c-Glow 模型建立 y 到隐变量 x 的关系。在网络结构上，c-Glow 模型将 glow 模型中的三个模块一一修改为 conditional 的模块。具体的形式比较简单，参见方法部分。

这里需要说明的是离散的 y 怎么计算概率。作者的做法是在训练的时候，在 y 的基础上加一个噪声使其连续化。推断的时候，根据连续值找到最近的离散值，这样得到输出。由于 c-Glow 模型得到的是 p(y|x)，是一个分布，要找到最终的输出 y，作者提供了两种方法。第一种是 gradient-based 方法，即通过对 z 的优化找到概率值最大的 y，第二种方法是 sample 多个 z，得到多个 y 值，然后取平均。作者发现第二种方法效果好，而且速度快，只需要 10 个样本就能得到很好的结果。而第一种方法很容易陷入局部最优，导致预测结果不好。

作者的实验有五个任务：二值分割、多类分割、去噪、Depth reﬁnement、image inpainting，一些详细的说明参见方法部分。

这篇文章思路还是很简单的，但是我觉得这种 conditional 的做法还是有很大的改进空间。



## 方法

首先要说明的是 c-Glow 模型的详细结构。

glow 模型有三个部分，actnorm，1x1 卷积，affine coupling。作者首先设计了一个网络 CN，用来从条件 x 提取特征或者作为参数网络的输入。改进后的三个模块的具体形式如下：

Conditional actnorm：
$$
\begin{equation}
s, b=\mathrm{CN}(x), \quad u_{i, j}=s \odot v_{i, j}+b
\end{equation}
$$
这里的 CN 是一个 3层卷积网络（有下采样）+3层全连接网络。

Condtional 1x1 卷积：
$$
\begin{equation}
s, b=\mathrm{CN}(x), \quad u_{i, j}=s \odot v_{i, j}+b
\end{equation}
$$


这里的 CN 是类似的六层网络，只不过最后一层的输出是矩阵的参数。

Contional affine layer:
$$
\begin{equation}
\begin{array}{ll}
v_{1}, v_{2}=\operatorname{split}(v), & x_{r}=\mathrm{CN}(x) \\
s_{2}, b_{2}=\mathrm{NN}\left(v_{1}, x_{r}\right), & u_{2}=s_{2} \odot v_{2}+b_{2} \\
u=\operatorname{concat}\left(v_{1}, u_{2}\right) &
\end{array}
\end{equation}
$$
在这里 CN 是一个三层的卷积网络。

在实验中，二值分割任务中，target 是分割结果（mask）的三个copy 构成的图像。去噪任务中，target 是噪声图片和真实图片的残差。image inpaiting 的 y 是缺失的区域（作者在原来的人脸图像中挖去了一个方形区域），x 是剩下的区域。效果看起来比 GAN 的方法好（不过不知道作者比较的方法是不是 sota）

