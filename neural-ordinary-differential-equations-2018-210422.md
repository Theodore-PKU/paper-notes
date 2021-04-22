# Neural Ordinary Differential Equations

[TOC]

## 信息

 Authors:

Year: NeurIPS 2018

Bibtex:

```
@article{chen2018neural,
  title={Neural ordinary differential equations},
  author={Chen, Ricky TQ and Rubanova, Yulia and Bettencourt, Jesse and Duvenaud, David},
  journal={arXiv preprint arXiv:1806.07366},
  year={2018}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/neural-ordinary-differential-equations.pdf)



## 概括

这篇文章提出了神经常微分方程的概念，可以理解成是另一种类型的神经网络，也可以看成是残差网络的某种拓展。在残差网络中，输入和输出之间的残差通过神经网络进行计算，这非常类似于常微分方程中的离散化计算。作者将这种离散化情形拓展到连续的情形，就变成了神经常微分方程的性质。

神经常微分方程的优点在于：1. 内存效率，因为这种方法相当于残差网络的每一层都用一个函数来表示了，所以即便网络很深（也就是求解常微分方程时步数更多）其内存消耗是不变的。2. 自适应计算，因为在求解常微分方程中可以有很多控制误差精度的技巧，对应不同的计算量，所以可以进行这方面的调整控制。3. 和 flow 模型完美契合。4. 可以用于连续时许分析。

在神经常微分方程中，其核心是用一个神经网络来建模 x 关于 t 的梯度函数（但是这篇文章似乎对于如何建模的说明非常少）。当这个函数已知的时候，使用任意的 ODE solver 就可以进行求解得到最终的输出（输入是 x_0，输出是 x_T）。比较关键的问题是如何训练网络参数，作者在论文中给出了较为详细的说明（但是有些地方没有理解）。另外我们主要关心 flow 模型，所以只详细看了这个部分。在 flow 模型中，神经常微分方程构成了连续 flow 模型，因此成为 CNF。在 flow 模型中，核心是概率变换公式，作者给出了一个定理，这样就可以比较快速的计算概率变换。但是如何训练网络参数呢？这里的损失函数比作者讨论的神经 ODE 的一般损失函数情形要复杂很多。（暂且不知道，可能得看一下其他文章）

作者在这篇文章中将神经常微分方程初步应用于监督学习、normalizing flow、连续时许模型等等。看过一篇相关的博客，里面对于神经常微分方程方法的评价是，虽然很新奇，但是目前还没有很多应用，使用这种方法的任务也相对简单。阅读这篇文章的主要原因是 score-based SDE 模型和神经微分方程存在紧密联系，所以通过这篇文章来理解神经常微分方程的基本思想以及如何应用于 flow 模型。



## 方法

残差模型可以看成是 $\mathbf{h}_{t+1}=\mathbf{h}_{t}+f\left(\mathbf{h}_{t}, \theta_{t}\right)$，时间 t 其实可以理解成每一层的深度。那么对应的常微分方程就有形式：
$$
\frac{d \mathbf{h}(t)}{d t}=f(\mathbf{h}(t), t, \theta)
$$
$f$ 相当于神经网络的某一层。$\theta$ 是网络的参数。假设 $f$ 已知，那么通过 ODE 求解 (1) with 初值（输入） $h(0)$，得到最终的输出 $h(T)$ 也就是问题的解。

**如何训练？**

假设损失函数是 L，那么有如下的形式：
$$
L\left(\mathbf{z}\left(t_{1}\right)\right)=L\left(\mathbf{z}\left(t_{0}\right)+\int_{t_{0}}^{t_{1}} f(\mathbf{z}(t), t, \theta) d t\right)=L\left(\operatorname{ODESolve}\left(\mathbf{z}\left(t_{0}\right), f, t_{0}, t_{1}, \theta\right)\right)
$$
为了优化 $L$，首先需要计算梯度 $a(t) = \frac{\part L}{\part z(t)}$，这个梯度函数满足：
$$
\frac{d \mathbf{a}(t)}{d t}=-\mathbf{a}(t)^{\top} \frac{\partial f(\mathbf{z}(t), t, \theta)}{\partial \mathbf{z}}
$$
其本身就是一个 ODE，但是我不太了解 (3) 是如何推导的。这个暂且跳过。那么 $L$ 关于 $\theta$ 的梯度是：
$$
\frac{d L}{d \theta}=-\int_{t_{1}}^{t_{0}} \mathbf{a}(t)^{\top} \frac{\partial f(\mathbf{z}(t), t, \theta)}{\partial \theta} d t
$$
为了计算 (4) 的积分，作者设计了一个训练流程，在计算 $a(t)$ 的同时可以很快速的计算出来。

![屏幕快照 2021-04-22 上午10.37.45](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-22 上午10.37.45.png)

这个算法其实写的很明确，而且确实很合理。

总结：只要 $f$ 的形式给定，损失函数给定，那么按照算法1 就可以对参数进行训练。这里有一个问题是如果损失函数不仅仅和 ODE 的解有关呢？考虑 flow 模型，在 flow 模型中我认为这个问题是存在的。

**连续 flow 模型**

基本的 flow 模型的形式是：
$$
\mathbf{z}_{1}=f\left(\mathbf{z}_{0}\right) \Longrightarrow \log p\left(\mathbf{z}_{1}\right)=\log p\left(\mathbf{z}_{0}\right)-\log \left|\operatorname{det} \frac{\partial f}{\partial \mathbf{z}_{0}}\right|
$$
考虑 $z_0$  到 $z_1$ 是一个常微分方程的某个解的两端。核心就是估计对数概率的差值。作者给了一个定理：

![屏幕快照 2021-04-22 上午10.43.21](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-22 上午10.43.21.png)

这个定理说明，是要计算 $-\mathrm{tr}\left(\dfrac{df}{dz(t)}\right)$ 的积分就可以得到概率变换公式了。因为极大似然概率是 flow 模型的目标函数，所以在 CNF 中，目标函数可以写成：
$$
\max \log p(z_0) + \int^T_0 -\mathrm{tr}\left(\dfrac{df}{dz(t)}\right) dt
$$
在这里初值似乎是 $z(T)$，也就是已知的图像。对于 ODE 而言，终点和起点是可以调换的。并且从 (6) 式可以看出，并不要求 $f$ 有特别复杂的形式，只要它是一个双射，满足定理1的一些条件即可。由于积分项通过离散化也是可以计算的，所以我猜测最后还是会以某种离散形式来计算，通过自动微分就可以算关于参数的梯度了。



## 反思

神经 ODE 这种方法，我认为本质就是将神经网络的变换变成一个关于时间 t 的网络，当 t 变动的时候对应不同的变换。实际在求解 ODE 的时候，还是需要离散化，只是这个离散化使用的每一层网络的形式都是 $f$，只不过时间 t 不一样了。但是这样也就意味着关于时间 t 的网络的表达能力要足够强，这个应该怎么实现呢？t 如何参与到计算中呢？我觉得这是这种方法的一个难点。另外虽然节约了内存，但是计算的整体开销仍然很大，因为当 t 取很多步的时候，和计算深层网络是一样的。

