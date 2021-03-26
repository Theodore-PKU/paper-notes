# VFlow: More Expressive Generative Flows with Variational Data Augmentation

[TOC]

## 信息

Authors: Jianfei Chen, Cheng Lu, Biqi Chenli, Jun Zhu, Tian Tian

Year: ICML 2020

Bibtex:

```latex
@inproceedings{chen2020vflow,
  title={Vflow: More expressive generative flows with variational data augmentation},
  author={Chen, Jianfei and Lu, Cheng and Chenli, Biqi and Zhu, Jun and Tian, Tian},
  booktitle={International Conference on Machine Learning},
  pages={1660--1669},
  year={2020},
  organization={PMLR}
}
```

cite: 8



## Code

https://github.com/thu-ml/vflow



## 概括

这篇文章想法挺奇特的，目标是增强 flow 模型的表达能力，想法是改进模型结构设计，使得 bottleneck 效应减弱。作者考虑到每个 step，在变量转换到时候，隐变量和信号 x 之间总是相同维度，而不是超过 x 的更多维度的 feature。作者的想法是扩充输入的维度，用一些随机变量的值来补充，增加信号的维数，相应的隐变量的维数也增加了。而多出来的隐变量，使得原来的训练损失函数不可用，作者采用了 VAE 的做法，用 ELBO 的损失函数，其中会设计到 q(z|x) 的概率估计，这里作者说用另一个 flow 模型来表示（甚至直接去掉）。

为了说明这种做法是优于原来的 flow 模型（因为作者认为这种做法扩充了 feature 的维度，减弱了 bottleneck 效应），作者试图证明 ELBO 损失函数的优化结果是优于原来的模型。但是我直觉上觉得有问题，最后发现在作者的证明中的确存在严重的漏洞，也就是说这个证明是不成立的。

总而言之，这篇文章提出的方法不值得参考。



## 方法

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-26 下午5.25.19.png" alt="屏幕快照 2021-03-26 下午5.25.19" style="zoom:50%;" />

通常的做法是 (a)，作者认为存在 bottleneck 的影响（这里的 flow 模型采用的是 flow++）。作者的做法就是 (b) 图，增加了 z，然后用 flow 模型去表示 (x, z)。如果只表示 (x, z)，概率计算是困难的。所以考虑下述的损失函数：
$$
\begin{equation}
\log p(\mathbf{x} ; \boldsymbol{\theta}) \geq \mathbb{E}_{q(\mathbf{z} \mid \mathbf{x} ; \boldsymbol{\phi})}[\log p(\mathbf{x}, \mathbf{z} ; \boldsymbol{\theta})-\log q(\mathbf{z} \mid \mathbf{x} ; \boldsymbol{\phi})]
\end{equation}
$$
类似于 VAE 的推导。这里的 $q(z|x; \phi)$ 用另一个 flow 模型实现。
$$
\begin{equation}
\log q(\mathbf{z} \mid \mathbf{x} ; \boldsymbol{\phi})=\log p_{\boldsymbol{\epsilon}}\left(\boldsymbol{\epsilon}_{q}\right)-\log \left|\frac{\partial \mathbf{z}}{\partial \boldsymbol{\epsilon}_{q}}\right|
\end{equation}
$$
而 x 的概率计算使用：
$$
\begin{equation}
\log p(\mathbf{x} ; \boldsymbol{\theta}) \approx \log \left(\frac{1}{S} \sum_{i=1}^{S} \frac{p\left(\mathbf{x}, \mathbf{z}_{i} ; \boldsymbol{\theta}\right)}{q\left(\mathbf{z}_{i} \mid \mathbf{x} ; \boldsymbol{\phi}\right)}\right)
\end{equation}
$$
下面是关于作者证明的错误分析。

作者首先给了两个假设：

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-26 下午8.45.00.png" alt="屏幕快照 2021-03-26 下午8.45.00" style="zoom:50%;" />

作者的推导是：在 A1，A2 条件下
$$
\begin{equation}
\max _{\boldsymbol{\theta}_{x} \in \Theta_{x}} \mathbb{E}_{\hat{p}(\mathbf{x})}\left[\log p_{x}(\mathbf{x} ; \boldsymbol{\theta})\right] 
=\max _{\boldsymbol{\theta}_{x} \in \Theta_{x}} \mathbb{E}_{\hat{p}(\mathbf{x})}\left[\log p_{a}\left(\mathbf{x}, \mathbf{z} ; \boldsymbol{\theta}\left(\boldsymbol{\theta}_{x}\right)\right)-\log p_{\boldsymbol{\epsilon}}(\mathbf{z})\right]
\end{equation}
$$

$$
\begin{equation}
\leq \max _{\boldsymbol{\theta}_{a} \in \Theta_{a}} \mathbb{E}_{\hat{p}(\mathbf{x})}\left[\log p_{a}\left(\mathbf{x}, \mathbf{z} ; \boldsymbol{\theta}_{a}\right)-\log p_{\boldsymbol{\epsilon}}(\mathbf{z})\right]
\end{equation}
$$

这里的 $\theta_a$ 指的是扩充 x 维度之后的大网络的参数，$\theta_x$ 是原来的参数。$\theta(\theta_x)$ 则是满足 A1 条件的参数。且不说这两个条件是否成立。如果他们成立，(5) 的推导没错。

但是作者接下来推导将 $\log p_\epsilon(z)$ 替换成了 $\log q(z|x; \phi)$，
$$
\begin{equation}
=\max _{\boldsymbol{\theta}_{a} \in \Theta_{a}} \mathbb{E}_{\hat{p}(\mathbf{x})}\left[\log p_{a}\left(\mathbf{x}, \mathbf{z} ; \boldsymbol{\theta}_{a}\right)-\log q(\mathbf{z} \mid \mathbf{x} ; \boldsymbol{\phi})\right]
\end{equation}
$$
我认为这是错误的。因为当 (5) 的 max 取到的时候，A2 条件就无法满足了。实际上 A1，A2 成立的前提是x 和 z 是独立，这也是为什么 (4) 成立。但是如果更换了其他参数，即使 x 和 z 独立，$\log q(z|x; \phi)$ 就不可能等于 $\log p_\epsilon(z)$。所以后续的推导就是错误的，因此作者的定理一（A1 A2 成立）：
$$
\begin{equation}
\begin{aligned}
& \max _{\boldsymbol{\theta}_{x} \in \mathbf{\Theta}_{x}} \mathbb{E}_{\hat{p}(\mathbf{x})}\left[\log p_{x}\left(\mathbf{x} ; \boldsymbol{\theta}_{x}\right)\right] \\
\leq & \max _{\boldsymbol{\theta}_{a} \in \boldsymbol{\Theta}_{a}, \boldsymbol{\phi} \in \boldsymbol{\Phi}} \mathbb{E}_{\hat{p}(\mathbf{x}) q(\mathbf{z} \mid \mathbf{x} ; \boldsymbol{\phi})}\left[\log p_{a}\left(\mathbf{x}, \mathbf{z} ; \boldsymbol{\theta}_{a}\right)-\log q(\mathbf{z} \mid \mathbf{x} ; \boldsymbol{\phi})\right]
\end{aligned}
\end{equation}
$$
是不对的。

关于实验，作者的实验是 CIFAR-10 的实验。比较对象就是 flow++，从效果上看，我觉得很差了。flow 模型值得改进还有很多，但绝不是这样的模型！