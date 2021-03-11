# Closed-Form Factorization of Latent Semantics in GANs

[TOC]

## 信息

Authors: Yujun Shen, Bolei Zhou

Year: arXiv 2020

Bibtex:

```
@article{shen2020closed,
  title={Closed-form factorization of latent semantics in gans},
  author={Shen, Yujun and Zhou, Bolei},
  journal={arXiv preprint arXiv:2007.06600},
  year={2020}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/closed-form-factorization-of-latent-semantics-in-gans.pdf)



## Code

https://genforce.github.io/sefa/



## 概括

这篇文章研究的是 GAN 模型的 latent space 的语义信息。采用的方法和其他的监督学习等方法不同，其他方法往往需要先 sample 出很多图像，然后再找到它们的 latent variable 来计算。这篇文章则直接分析 latent variable 经过全连接层之后的变动，直接通过全连接层的权重矩阵的奇异向量作为语义信息（这里的语义信息实际是向量，一个方向）。作者用这种方法确实可以发现不同的方向确实具有具体语义。



## 方法

现在常见的 GAN 方法，不论是 styleGAN 还是其他方法，latent variable 都会经过一个全连接网络。然后要么作为主干网络的 input，要么参与 normalization 的计算。所以这个全连接网络（可能只有一层）的权重可以用来分析。假设只有一层，那么就是一个矩阵。作者认为输出图像的差异，可以从全连接层的输出的变化入手。当隐变量改变的时候，$z + \alpha \mathbb{n}$，输出也相应改变。设矩阵为 $A$, bias 为 $b$，那么就有：
$$
\begin{equation}
\Delta \mathbf{y}=F C\left(\mathbf{z}^{\prime}\right)-F C(\mathbf{z})=(\mathbf{A}(\mathbf{z}+\alpha \mathbf{n})+\mathbf{b})-(\mathbf{A} \mathbf{z}+\mathbf{b})=\alpha \mathbf{A} \mathbf{n}
\end{equation}
$$
那么为了找到变化的最大情况，问题就变成一个优化问题，找到最大的 k 个方向：
$$
\begin{equation}
\mathbf{N}^{*}=\underset{\left\{\mathbf{N} \in \mathbb{R}^{d \times k}: \mathbf{n}_{i}^{T} \mathbf{n}_{i}=1 \forall i=1, \cdots, k\right\}}{\arg \max } \sum_{i=1}^{k}\left\|\mathbf{A} \mathbf{n}_{i}\right\|_{2}^{2}
\end{equation}
$$
这个问题可以用拉格朗日乘子法解决，结果就是 $A^TA$ 的 k 个最大特征向量。

比较有意思的实验结果是 styleGAN

![屏幕快照 2021-03-11 下午3.05.18](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-11 下午3.05.18.png)

不同层的语义方向是不同。这一点某种意义上验证了 styleGAN 的想法的合理性。



## 反思

这篇文章分析的 GAN 的隐变量的语义信息。虽然和生成模型的关系不大，但是似乎对于如何理解隐变量和生成图像之间的关系有借鉴意义。在 GAN 的隐变量空间中，似乎不是一个空间关系，更像是一个字典。不同方向的语义会被全连接层的权重分离出来，即使最开始是混在一个向量之中。但是和字典也有些不同，字典的表示下，每个分量本身就是有意义的，但是在 GAN 里，并不是分量有意义，而是原向量分解之后有意义。隐变量表示的信息是什么？如何参与到网络中并且起作用，这个问题不论是哪个生成模型都是一个需要思考的问题。

