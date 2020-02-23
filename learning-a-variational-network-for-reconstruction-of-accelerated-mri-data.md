### Paper:

Learning a Variational Network for Reconstruction of Accelerated MRI Data

### Author:

Kerstin Hammernik, Teresa Klatzer, Erich Kobler, Michael P. Recht,  Daniel K. Sodickson, Thomas Pock,  and Florian Knoll

### Year:

2018

### Notes:

精读。这篇文章引用率很高，想法和ADMM-net很类似，但是展开的算法不同（基于 Fields of Experts model 的迭代算法），而且这篇文章比较详细地讨论了复数的情形。文章考虑的是多线圈MRI的重建。

首先，说明复数的处理：
$$
\tilde{\mathbf{u}}=\mathbf{u}_{\mathrm{re}}+\mathbf{j} \mathbf{u}_{\mathrm{im}} \in \mathbb{C}^{N} \Longleftrightarrow \mathbf{u}=\left(\mathbf{u}_{\mathrm{re}}, \mathbf{u}_{\mathrm{im}}\right) \in \mathbb{R}^{2 N}
$$
也就是说，作者把复数域的计算换成实数域的写法。模型为 $\mathbf{A} \mathbf{u}=\hat{\mathbf{f}}$，$\hat{\mathbf{f}}$ 是 undersampled kspace data。$\mathbf{A}$ 表示了傅立叶变换、sensitive map 和欠采样scheme。求解 $\mathbf{u}$ ，作者考虑的是由正则项的最小二乘目标函数，最终作者使用的随迭代次数变化的 Fields of Experts model 正则项。
$$
\mathcal{R}(\mathbf{u})=\sum_{i=1}^{N_{k}}\left\langle\Phi_{i}\left(\mathbf{K}_{i} \mathbf{u}\right), \mathbf{1}\right\rangle
$$
每次迭代，$\mathcal{R}(\mathbf{u})$ 的参数都不相同，如果用一般的梯度法，可以得到：
$$
\mathbf{u}^{t+1}=\mathbf{u}^{t}-\sum_{i=1}^{N_{k}}\left(\mathbf{K}_{i}^{t}\right)^{\top} \Phi_{i}^{t \prime}\left(\mathbf{K}_{i}^{t} \mathbf{u}^{t}\right)-\lambda^{t} \mathbf{A}^{*}\left(\mathbf{A} \mathbf{u}^{t}-\mathbf{f}\right), \quad 0 \leq t \leq T-1
$$
在这里，$\Phi^t$ 其实是激活函数（shrinkage），$\mathbf{K}_i$ 是卷积滤波器，考虑复数用实数表示，实际的计算是：
$$
\begin{aligned}
\mathbf{K} \mathbf{u} &=\mathbf{K}_{\mathrm{re}} \mathbf{u}_{\mathrm{re}}+\mathbf{K}_{\mathrm{im}} \mathbf{u}_{\mathrm{im}} \\
\mathbf{u} & \in \mathbb{R}^{2 N} \Longleftrightarrow \mathbf{u} * k=u_{\mathrm{re}} * k_{\mathrm{re}}+u_{\mathrm{im}} * k_{\mathrm{im}}, \quad u \in \mathbb{R}^{n_{\mathrm{x}} \times n_{\mathrm{y}} \times 2}
\end{aligned}
$$
作者的做法就是基于 (3) 式展开成网络，所有的参数均可以学习，包括 $\mathbf{K},\Phi,\lambda$ 等，这里省略了步长，是因为作者认为可以由卷积和激活函数来表示。

整体框架和卷积部分的计算细节可以从图上看出来：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-03%E4%B8%8B%E5%8D%886.51.15.png" style="zoom:50%;" />

下面对一些细节进行说明：卷积强制为范数为1，$\left\|\mathbf{k}_{i}^{t}\right\|_{2}=1$ ，实际迭代的时候只有 $\Phi^{'}$，作者用高斯径向基函数来表示：
$$
\phi_{i}^{t \prime}(z)=\sum_{j=1}^{N_{w}} w_{i j}^{t} \exp \left(-\frac{\left(z-\mu_{j}\right)^{2}}{2 \sigma^{2}}\right)
$$
这里的 $\mu, \sigma$ 取决于当前的卷积相应。$\mathbf{\sigma}=\frac{2 I_{\max }}{N_{w}-1}$. $\lambda > 0$. 所有对参数的限制，都可以在训练时通过project的方式处理。

训练时的目标函数基于 magnitude 而不是两通道的值，
$$
\mathcal{L}(\theta)=\min _{\theta} \frac{1}{2 S} \sum_{s=1}^{S}\left\|\left.\mathbf{u}_{s}^{T}(\theta)\right|_{\epsilon}-\left|\mathbf{g}_{s}\right|_{\epsilon}\right\|_{2}^{2},|\mathbf{x}|_{\epsilon}=\sqrt{\mathbf{x}_{\mathrm{re}}^{2}+\mathbf{x}_{\mathrm{im}}^{2}+\epsilon}
$$
训练时的优化算法是 Inertial Incremental Proximal Gradient (IIPG) optimizer。Coil sensitivity maps 事先用 ESPIRiT 估计好。网络的迭代初值选择 zero-fill 重建结果。训练样本取每个病人的中间20张slice。

> 思考：作者在讨论部分提到了ReLU这种激活函数因为是固定，要达到一样的效果可能要更多的层。作者的意思似乎是固定的shrinkage函数可能不够灵活？另一方面，在其他的深度学习方法中，激活函数如何用迭代算法中的 shrinkage 函数来理解呢？



### Bibtex:

```latex
@article{hammernik2018learning,
  title={Learning a variational network for reconstruction of accelerated MRI data},
  author={Hammernik, Kerstin and Klatzer, Teresa and Kobler, Erich and Recht, Michael P and Sodickson, Daniel K and Pock, Thomas and Knoll, Florian},
  journal={Magnetic resonance in medicine},
  volume={79},
  number={6},
  pages={3055--3071},
  year={2018},
  publisher={Wiley Online Library}
}
```