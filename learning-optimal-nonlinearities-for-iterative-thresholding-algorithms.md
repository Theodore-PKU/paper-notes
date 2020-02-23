### Paper:

Learning optimal nonlinearities for iterative thresholding algorithms

### Author:

Ulugbek S. Kamilov and Hassan Mansour

### Year:

2016

### Notes:

这篇论文思路很简单，就是将ISTA算法中的软阈值约束这一步使用的proximal 算子替换为一个训练的非线性函数。这里非线性函数是用三次样条的基函数表示的。训练过程就是给定T次迭代，使得最终重建结果和真实值的二次范数loss最小。不过在这样的设定条件下，本来的正则项就消失了，在计算过程中不存在，见下文分析。

ISTA算法在逆问题里很常见。设模型 $\mathbf{y}=\mathbf{H} \mathbf{x}+\mathbf{e}$, 正则项 $\mathcal{R}(\mathbf{x})$，一般的迭代过程为：
$$
\begin{array}{l}{\mathbf{z}^{t} \leftarrow\left(\mathbf{I}-\gamma \mathbf{H}^{\mathrm{T}} \mathbf{H}\right) \mathbf{x}^{t-1}+\gamma \mathbf{H}^{\mathrm{T}} \mathbf{y}} \\ {\mathbf{x}^{t} \leftarrow \mathcal{T}\left(\mathbf{z}^{t} ; \gamma \lambda\right)}\end{array}
$$
 第二步取决于正则项：
$$
\begin{aligned} \mathcal{T}(z ; \lambda) &=\operatorname{prox}_{\gamma \mathcal{R}}(z) \\ & \triangleq \underset{x \in \mathbb{R}}{\arg \min }\left\{\frac{1}{2}(x-z)^{2}+\lambda \mathcal{R}(x)\right\} \end{aligned}
$$
现在作者考虑用训练数据学习一个非线性函数替代第二步。替代之后，正则项就没有什么影响了，不过 $\gamma$ 却保留了。

首先重新改写算法形式，利用 $\mathbf{s} \triangleq \mathbf{I}-\gamma \mathbf{H}^{\mathrm{T}} \mathbf{H}, \mathbf{b} \triangleq \gamma \mathbf{H}^{T} \mathbf{y}$，原来的迭代可以写为：
$$
\begin{array}{l}{z_{m}^{t} \leftarrow \sum_{n=1}^{N} S_{m n} x_{n}^{t-1}+b_{m}} \\ {x_{m}^{t} \leftarrow \varphi\left(z_{m}^{t}\right)}\end{array}
$$
$m \in [1, ..., N]$，这里 $\mathbf{x}$ 就先看成向量。作者设计的非线性函数是三次样条的组合，最终是要训练基函数的系数。也就是对系数进行优化，优化方法就是求导。非线性函数的形式为：
$$
\varphi(z) \triangleq \sum_{k=-K}^{K} c_{k} \psi\left(\frac{z}{\Delta}-k\right)
$$
这里似乎是考虑 $\mathbf{x}$ 的一个小邻域。

损失函数是重建二范数。$\mathcal{E}(\mathbf{c}, \mathbf{x}, \mathbf{y}) \triangleq \frac{1}{2}\left\|\mathbf{x}-\mathbf{x}^{T}(\mathbf{c}, \mathbf{y})\right\|_{\ell_{2}}^{2}$ 这里的T表示T次迭代的结果。为了简单起见，只算一个样本。
$$
\mathcal{E}(\mathbf{c}) \triangleq \frac{1}{2}\left\|\mathbf{x}-\mathbf{x}^{T}(\mathbf{c})\right\|_{\ell_{2}}^{2}=\frac{1}{2} \sum_{m=1}^{N}\left(x_{m}-x_{m}^{T}(\mathbf{c})\right)^{2}\\
\text{现在的首要目标是计算对c的导数，因为多次迭代，所以会涉及到} x_m^t 对c的导数。\\
\nabla \mathcal{E}(\mathbf{c})=\left[\frac{\partial}{\partial \mathbf{c}} \mathbf{x}^{T}(\mathbf{c})\right]^{\mathrm{T}}\left(\mathbf{x}^{T}(\mathbf{c})-\mathbf{x}\right)\\
上式式对损失函数的第一次求导，后半部分不用进一步计算。\\
\begin{aligned} \frac{\partial}{\partial \mathbf{c}} \mathbf{x}^{t}(\mathbf{c}) & \triangleq\left[\frac{\partial \mathbf{x}^{t}}{\partial c_{-K}} \ldots \frac{\partial \mathbf{x}^{t}}{\partial c_{K}}\right] \\ &=\left[\begin{array}{ccc}{\frac{\partial x_{1}^{t}}{\partial c_{-K}}} & {\cdots} & {\frac{\partial x_{t}^{t}}{\partial c_{K}^{t}}} \\ {\vdots} & {\ddots} & {\vdots} \\ {\frac{\partial x_{N}^{t}}{\partial c_{-K}}} & {\cdots} & {\frac{\partial x_{N}^{t}}{\partial c_{K}}}\end{array}\right] \end{aligned}\\
所以我们要算 {\frac{\partial x_{m}^{t}}{\partial c_{k}}}\\
x_m^t = \varphi(z_m^t, c)  = \varphi(\sum_{n=1}^{N} S_{m n} x_{n}^{t-1}+b_{m}, c)\\
\frac{\partial x_{m}^{t}}{\partial c_{k}}= \frac{\part{\varphi}}{\part c_k} + \frac{\part{\varphi}}{\part z_m^t} \frac{\part z_m^t}{c_k} \\
\frac{\partial x_{m}^{t}}{\partial c_{k}}=\psi\left(\frac{z_{m}^{t}} {\Delta}-k\right)+\varphi^{\prime}\left(z_{m}^{t}\right) \sum_{n=1}^{N} S_{m n}\left[\frac{\partial x_{n}^{t-1}}{\partial c_{k}}\right] \\
记 \psi\left(\frac{z_{m}^{t}} {\Delta}-k\right) = \Psi_{m k}^{t}
$$

$$
因为在第一次求导的计算中，需要计算内积，因此设有向量 \mathbf{r}\\
\begin{aligned} \sum_{m=1}^{N}\left[\frac{\partial x_{m}^{t}}{\partial c_{k}}\right] r_{m} &=\sum_{m=1}^{N} \Psi_{m k}^{t} r_{m} \\ &+\sum_{n=1}^{N}\left[\frac{\partial x_{n}^{t-1}}{\partial c_{k}}\right] \sum_{m=1}^{N} S_{m n} r_{m} \varphi^{\prime}\left(z_{m}^{t}\right) \end{aligned}\\
可以改写成\\
\left[\frac{\partial \mathbf{x}^{t}}{\partial \mathbf{c}}\right]^{\mathrm{T}} \mathbf{r}=\left[\mathbf{\Psi}^{t}\right]^{\mathrm{T}} \mathbf{r}+\left[\frac{\partial \mathbf{x}^{t-1}}{\partial \mathbf{c}}\right]^{\mathrm{T}} \mathbf{S}^{\mathrm{T}} \operatorname{diag}\left(\varphi^{\prime}\left(\mathbf{z}^{t}\right)\right) \mathbf{r}\\
考虑第一次求导的内容\\
\nabla \mathcal{E}(\mathbf{c})=\left[\frac{\partial}{\partial \mathbf{c}} \mathbf{x}^{T}(\mathbf{c})\right]^{\mathrm{T}}\underbrace{\left(\mathbf{x}^{T}(\mathbf{c})-\mathbf{x}\right)}_{\stackrel{\triangleq}{=} \mathbf{r}^{T}}\\
=\underbrace{\left[\mathbf{\Psi}^{T}\right]^{\mathrm{T}} \mathbf{r}^{T}}_{\triangleq \mathbf{g}^{T-1}}+\left[\frac{\partial \mathbf{x}^{T-1}}{\partial \mathbf{c}}\right]^{\mathrm{T}} \underbrace{\mathbf{S}^{\mathrm{T}} \operatorname{diag}\left(\varphi^{\prime}\left(\mathbf{z}^{T}\right)\right) \mathbf{r}^{T}}_{\stackrel{\triangleq}{=} \mathbf{r}^{T-1}}\\
=\mathbf{g}^{T-1}+\left[\frac{\partial \mathbf{x}^{T-1}}{\partial \mathbf{c}}\right]^{\mathrm{T}} \mathbf{r}^{T-1}\\
= \underbrace{\mathbf{g}^{T-1}+\left[\mathbf{\Psi}^{T-1}\right]^{\mathrm{T}} \mathbf{r}^{T-1}}_{\triangleq \mathbf{g}^{T-2}} + \left[\frac{\partial \mathbf{x}^{T-2}}{\partial \mathbf{c}}\right]^{\mathrm{T}} \underbrace{\mathbf{S}^{\mathrm{T}} \operatorname{diag}\left(\varphi^{\prime}\left(\mathbf{z}^{T-1}\right)\right) \mathbf{r}^{T-1}}_{\triangleq \mathbf{r}^{T-2}} = ...\\
=\mathbf{g}^{0}+\left[\frac{\partial \mathbf{x}^{0}}{\partial \mathbf{c}}\right]^{\mathrm{T}} \mathbf{r}^{0}=\mathbf{g}^{0}
$$

我们有 $\dfrac{\partial \mathbf{x}^{0}}{\partial \mathbf{c}}=0$。因此就可以用这种递推的方法计算 $\nabla \mathcal{E}(\mathbf{c})$.  考虑到对c有凸集的约束，所以使用 projected gradient iteration，投影梯度法。
$$
\mathbf{c}^{i}=\operatorname{proj}_{\mathcal{C}}\left(\mathbf{c}^{i-1}-\mu \nabla \mathcal{E}\left(\mathbf{c}^{i-1}\right)\right)
$$
最后给出算法的计算过程。

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-15%E4%B8%8B%E5%8D%883.41.58.png)

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-15%E4%B8%8B%E5%8D%883.42.07.png)

下图是训练出来的非线性函数。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-15%E4%B8%8B%E5%8D%883.42.15.png" style="zoom:67%;" />

最后需要指出的是作者没有明确说明和 $K, \Delta$ 有关的内容

### Bibtex:

```latex
@article{kamilov2016learning,
  title={Learning optimal nonlinearities for iterative thresholding algorithms},
  author={Kamilov, Ulugbek S and Mansour, Hassan},
  journal={IEEE Signal Processing Letters},
  volume={23},
  number={5},
  pages={747--751},
  year={2016},
  publisher={IEEE}
}
```