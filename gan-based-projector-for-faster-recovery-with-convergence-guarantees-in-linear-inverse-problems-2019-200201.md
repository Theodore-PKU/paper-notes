### Paper:

GAN-based Projector for Faster Recovery with Convergence Guarantees in Linear Inverse Problems

### Author:

Ankit Raj, Yuqi Li, Yoram Bresler

### Year:

2019

### Notes:

阅读时间：2020.02.01

精读。这篇文章研究的是一般的逆问题的重建，主要研究的是压缩感知。作者使用的方法是基于 GAN，思路一定程度上沿袭了 Bora 的做法，不过这篇文章又有很大的不同，在整体思路上和我的想法非常一致。作者把训练好的 GAN 当作是图像的刻画，其值域空间就是图像空间。因为 Bora 那种方法非常费时，也收到局部最优点的限制。另一篇利用 GAN 的文章使用的是算法和 Bora 不同，作者借用了这篇文章的做法，但是把子循环中的梯度迭代求解过程换成了投影变换，即把信号直接映射到 GAN 的值域空间中。为此，作者设计了一个网络作为 GAN 的逆网络，通过特殊的损失函数来训练，这个函数和 GAN 的生成器构成了投影网络。有了投影网络，就可以实现我之前自己所思考的那种迭代方式了。

除了这个 GAN 的计算框架，作者还讨论了测量矩阵 A 的性质，作者给出了这种算法的收敛条件，指出一般的高斯随机矩阵很难满足这个收敛条件，因此提出了根据数据设计测量矩阵的方法，作者根据数学推导，得到了一个较为合适的计算 A 的优化目标函数，作者的实验证明确实更有效。另外，作者还考虑了超分辨和 inpainting 的任务，指出这两个任务的测量矩阵都不满足收敛条件（特别是当超分辨的比例和 inpainting 的空白区域太大的时候）。

设生成器是 $G$，latent variable 是 $z$，

Bora 的方法是：

<img src="http://latex.codecogs.com/svg.latex? \hat{z}=\arg \min _{z \in \mathbb{R}^{k}} \| y-A G(z)\left\|^{2}+\lambda\right\| z \|^{2}" border="0"/>

PGD-GAN 则是：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-01%E4%B8%8B%E5%8D%888.22.41.png" width="60%"/>

作者的思路则是 ($G_{\theta}^{\dagger}$表示生成器的逆，将信号变成 latent variable，用 $GG_{\theta}^{\dagger}$ 表示投影变换 $P_G$)：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-01%E4%B8%8B%E5%8D%888.29.33.png"/>

可以发现，作者提出的投影变换就是替换了 PGD 中的子循环，直接得到一个 R(G) 中的值。对于投影变换，必须满足两个条件，一个是 $P S (P S (x)) = P S (x)$，一个是 $P_{S}(\tilde{x})=\arg \min _{x \in S}\|x-\tilde{x}\|^{2}$，为此，作者提出了如下的损失函数来训练 $G_{\theta}^{\dagger}$，

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \mathcal{L}(\theta)=& \mathbb{E}_{z, \nu}\left[\left\|G\left(G_{\theta}^{\dagger}(G(z)+\nu)\right)-G(z)\right\|^{2}\right] \\ &+\mathbb{E}_{z, \nu}\left[\lambda\left\|G_{\theta}^{\dagger}(G(z)+\nu)-z\right\|^{2}\right] \end{aligned}" border="0"/>

第一项相当于 R(G) 经过投影还是 R(G)，第二项虽然也能理解为将 R(G) 之外的点投影到 R(G) 上，但是 R(G) 之外的点有限。不过作者用这个方式可以确定要投影到哪里去，也可以表示对 G_theta 的逆的训练。

另外，在理论方面，作者定义了 Restricted Eigenvalue Constraint (REC）：

<img src="http://latex.codecogs.com/svg.latex? \alpha\left\|x_{1}-x_{2}\right\|^{2} \leq\left\|A\left(x_{1}-x_{2}\right)\right\|^{2} \leq \beta\left\|x_{1}-x_{2}\right\|^{2}" border="0"/>

也定义了投影变换的好坏 $\delta$ 近似：

<img src="http://latex.codecogs.com/svg.latex? \left\|x-G\left(G^{\dagger}(x)\right)\right\|^{2} \leq \min _{z \in \mathbb{R}^{k}}\|x-G(z)\|^{2}+\delta" border="0"/>

给出了收敛的条件：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-01%E4%B8%8B%E5%8D%888.34.31.png" width="70%"/>

从定理中可以看出 $\beta/\alpha$ 很关键，因此作者推导了如何计算。设：

<img src="http://latex.codecogs.com/svg.latex? \mathcal{S}(G)=\left\{\frac{x_{1}-x_{2}}{\left\|x_{1}-x_{2}\right\|}: x_{1}, x_{2} \in R(G)\right\}" border="0"/>

因此：

<img src="http://latex.codecogs.com/svg.latex? z_{1}, z_{2} \sim \mathcal{N}\left(0, I_{k}\right), s=\frac{G\left(z_{1}\right)-G\left(z_{2}\right)}{\left\|G\left(z_{1}\right)-G\left(z_{2}\right)\right\|} \sim \Pi_{S}" border="0"/>

则有：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\min _{A \in \mathbb{R}^{m \times n}} \frac{\beta}{\alpha}=\min _{A \in \mathbb{R}^{m \times n}} \dfrac{\max _{s \in \mathcal{S}(G)}\|A s\|^{2}}{\min _{s \in \mathcal{S}(G)}\|A s\|^{2}}} \\ {\leq \min _{A A^{T}=I_{m}} \dfrac{1}{\min _{s \in \mathcal{S}(G)}\|A s\|^{2}}=\left(\max _{A A^{T}=I_{m}} \min _{s \in \mathcal{S}(G)}\|A s\|^{2}\right)}\end{array}" border="0"/>

因此，得到一个近似的做法:

<img src="http://latex.codecogs.com/svg.latex? A=\underset{A A^{T}=I_{m}}{\arg \max } E_{s \sim \Pi_{S}}\left[\|A s\|^{2}\right] \approx \underset{A A^{T}=I_{m}}{\arg \max } \frac{1}{M} \sum_{j=1}^{M}\left\|A s_{j}\right\|^{2}" border="0"/>

这里的 M 表示数据的量，也就是可以用数据集来估计。假设有足够多的数据，我们就可以用下式来得到一个比较好的 A:

<img src="http://latex.codecogs.com/svg.latex? A^{*}=\underset{A}{\arg \max }\|A D\|_{F}^{2} \text { s.t. } A A^{T}=I_{m}" border="0"/>

其解为 $DD^T$ 的 m 个最大特征向量。至此，这篇文章的理论部分结束。

下图是 $\|As\|^2$ 的一个分布图（可以看到随机的矩阵确实不满足作者提出的收敛条件）：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-01%E4%B8%8B%E5%8D%888.43.2.png" width="70%"/>

> 虽然投影的具体做法还不确定这是不是最好的，但是这篇文章已经很有启发了

### Bibtex:

```latex
@inproceedings{raj2019gan,
  title={GAN-Based Projector for Faster Recovery With Convergence Guarantees in Linear Inverse Problems},
  author={Raj, Ankit and Li, Yuqi and Bresler, Yoram},
  booktitle={Proceedings of the IEEE International Conference on Computer Vision},
  pages={5602--5611},
  year={2019}
}
```

