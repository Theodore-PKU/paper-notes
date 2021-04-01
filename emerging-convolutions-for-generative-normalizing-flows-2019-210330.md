# Emerging Convolutions for Generative Normalizing Flows

[TOC]

## 信息

Authors:

Year: ICML 2019

Bibtex:

```
@inproceedings{hoogeboom2019emerging,
  title={Emerging convolutions for generative normalizing flows},
  author={Hoogeboom, Emiel and Van Den Berg, Rianne and Welling, Max},
  booktitle={International Conference on Machine Learning},
  pages={2771--2780},
  year={2019},
  organization={PMLR}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/emerging-convolutions-for-generative-normalizing-flows.pdf)

cite: 48



## 概括

这篇文章研究的是如何替代 glow 模型中的 1x1 卷积。glow 模型使用 1x1 卷积作为可逆变换的其中一步。作者在这篇文章试图用其他形式的、有着更大感受野的卷积来替代 1x1 卷积。作者提出了两种类型。第一种是由两个自回归卷积（这个有点忘了具体是怎么做的）的复合构成。每个自回归卷积都是可逆的，复合的结构也仍然是可逆的。第二种是周期卷积，实际上是频率域上的卷积。具体的形式通过傅立叶变换，然后直接使用 element-wise 乘法，然后再用傅立叶逆变换得到结果。另外作者还考虑了 1x1 卷积的另一种分解形式。在原来的方法中，考虑的形式是 PLU 分解，作者在这里使用 QR 分解，其中 Q 是一个正交矩阵，这个矩阵可以由多个 Householder reflections 构成。

这篇文章的研究内容我不是很在意，我认为在 flow 模型中这不是核心问题。而且 1x1 卷积的具体含义，可能是无法被 dxd卷积替代。更何况，这篇文章的实验结果，我并没有看出比 1x1 卷积好。



## 方法

这里对文章提出的三个卷积都进行一些说明。

回归卷积：

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-01 下午8.12.10.png" alt="屏幕快照 2021-04-01 下午8.12.10" style="zoom:50%;" />

回归卷积对应一个三角矩阵，所以 Jacobian 矩阵的行列式可以计算。

作者考虑了不同的回归卷积组合，其中有一种组合可以让最终的效果等价于一个 3x3 卷积（当然不是完全等价）

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-01 下午8.13.25.png" alt="屏幕快照 2021-04-01 下午8.13.25" style="zoom:50%;" />

两个回归卷积复合以后，相当于：

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-01 下午8.13.35.png" alt="屏幕快照 2021-04-01 下午8.13.35" style="zoom:50%;" />

论文中作者把卷积的计算改成了矩阵乘法的形式，但是我没有验证这个是不是对的。

周期卷积（periodic convolutions）：这个卷积的计算方式在概括中已经说明了，但是我不太理解的地方是傅立叶变换的范围是多大，似乎和感受野的大小有关。

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-01 下午8.15.33.png" alt="屏幕快照 2021-04-01 下午8.15.33" style="zoom:50%;" />

这里的右图是频率域。

而 QR 分解的 1x1 卷积，Q 是一个正交矩阵，P 是一个三角矩阵。Q 的构造方式是：
$$
\begin{equation}
\mathbf{Q}=\mathbf{Q}_{1} \mathbf{Q}_{2} \ldots \mathbf{Q}_{n}
\end{equation}
$$
Q_i 是 Householder reflection (不知道是什么)
$$
\begin{equation}
\mathbf{Q}_{i}=\mathbf{I}-2 \frac{\mathbf{v}_{i} \mathbf{v}_{i}^{T}}{\mathbf{v}_{i}^{T} \mathbf{v}_{i}}
\end{equation}
$$
这里的 $n = n_c$，$v_i$ 是可学习的参数，这和 PLU 分解不同，PLU 分解中 P 是固定的，所以不够灵活（作者的观点）。QR 分解相当于是用可学习参数构建出一个可逆的 1x1 卷积。