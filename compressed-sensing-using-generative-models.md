### Paper:

Compressed Sensing using Generative Models

### Author:

Ashish Bora, Ajil Jalal, Eric Price, Alexandros G. Dimakis

### Year:

2017

### Notes:

这一篇文章的主要想法是假设有一个好的生成模型，只要在这个生成模型中搜索极小化压缩感知误差的信号就可以了（信号是由生成模型生成的）。

设生成模型 $G: \mathbb{R}^{k} \rightarrow \mathbb{R}^{n}$, 将低维的变量 $z$ 映射到信号空间中，并且 $G(z)$ 满足信号的分布，那么极小化损失函数 $\operatorname{loss}(z)=\|A G(z)-y\|^{2}$ 得到的 $z^*$ 对应的 $G(z^*)$ 就是一个比较好的重建结果。作者还考虑了在损失函数中加入一个关于 $z$  正则项。使用的生成模型可以是 GAN 也可以是 VAE。

文章中还给出了两个定理（并给出了证明），关于多层神经网络（使用 ReLU 激活函数）和一般生成模型，按照作者提出的方法得到的信号重建结果的误差上界。

**定理 1.** $G: \mathbb{R}^{k} \rightarrow \mathbb{R}^{n}$ 是一个 d 层使用 ReLU 的神经网络， $A\in \mathbb{R}^{m\times n}$ 是一个高斯随机矩阵，且 $A_{i,j}\sim N(0,1/m)， m=O(kd\log n)$，对于任意 $x^* \in \mathbb{R}^n$, $y = Ax^*+\eta$，令 $\hat{z}$ 是极小化 $\|y - AG(z)\|_2$ with additive $\epsilon$ of optimum，那么以 $1-e^{-\Omega(m)}$ 的概率有：
$$
\left\|G(\widehat{z})-x^{*}\right\|_{2} \leq 6 \min _{z^{*} \in \mathbb{R}^{k}}\left\|G\left(z^{*}\right)-x^{*}\right\|_{2}+3\|\eta\|_{2}+2 \epsilon
$$
**定理 2.** $G: \mathbb{R}^{k} \rightarrow \mathbb{R}^{n}$ 是一个 L-Lipschitz 函数， $A\in \mathbb{R}^{m\times n}$ 是一个高斯随机矩阵，且 $A_{i,j}\sim N(0,1/m)， m=O(k\log \frac{Lr}{\delta})$，对于任意 $x^* \in \mathbb{R}^n$, $y = Ax^*+\eta$，令 $\hat{z}$ 是极小化 $\|y - AG(z)\|_2$ with additive $\epsilon$ of optimum，且 $\|\hat{z}\|_2 \leq r$，那么以 $1-e^{-\Omega(m)}$ 的概率有： 
$$
\left\|G(\widehat{z})-x^{*}\right\|_{2} \leq 6 \min _{z^{*} \in \mathbb{R}^{k}, \|z^*\|_2 \leq r}\left\|G\left(z^{*}\right)-x^{*}\right\|_{2}+3\|\eta\|_{2}+2 \epsilon+2 \delta
$$

> 这一项是最困难的地方，如果生成器不能生成信号所在的低维流形，那么这种方法也很难得到很好的结果。另外如果 k 很小，虽然对应的测量值也会变小，但是G(z)的表达能力可能就会变差。

### Bibtex:

```latex
@inproceedings{bora2017compressed,
  title={Compressed sensing using generative models},
  author={Bora, Ashish and Jalal, Ajil and Price, Eric and Dimakis, Alexandros G},
  booktitle={Proceedings of the 34th International Conference on Machine Learning-Volume 70},
  pages={537--546},
  year={2017},
  organization={JMLR. org}
}
```

