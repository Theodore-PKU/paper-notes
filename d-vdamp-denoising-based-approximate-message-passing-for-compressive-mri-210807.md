# D-VDAMP: Denoising-based Approximate Message Passing for Compressive MRI

[TOC]

## 信息

Authors: Christopher A. Metzler, Gordon Wetzstein

Year: ICASSP 2021

Bibtex:

```latex
@inproceedings{metzler2021d,
  title={D-vdamp: Denoising-based approximate message passing for compressive mri},
  author={Metzler, Christopher A and Wetzstein, Gordon},
  booktitle={ICASSP 2021-2021 IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP)},
  pages={1410--1414},
  year={2021},
  organization={IEEE}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/d-vdamp-denoising-based-approximate-message-passing-for-compressive-mri.pdf)



## 概括

这篇文章是关于 DAMP 的求解方法。DAMP 是一个研究了比较久的模型，用于求解类似于 MRI 重建这样的计算成像问题。作者的逻辑是：DAMP 方法要求 effective noise 是高斯的，但是在 MRI 重建情形下不满足；有人提出了 VDAMP 方法，此时的 effective noise 是 colored gaussian，这个噪声是在小波域上度量的。作者则改进了这个方法，通过引入一个 CNN-based colored noise removal 模型，代替了小波域上的阈值。这个模型就是一个去噪模型，VDAMP 算法本质上和 AMP 算法差不多，只不过噪声的估计是在小波域上进行的，而且小波域上不同的 band 的噪声不同，这对应训练的时候加噪声也是在不同的 band 上加，而且噪声水平也不相同。这应该就是所谓 colored gaussian noise。

这篇文章最后说的不确定性应该是指 MAP 的不确定性，而不是后验分布的不确定性，另外，作者的 colored gaussian 的假设似乎是用实验的结果来验证的，不知道这个理论上怎么说明。



## 方法

整个算法：

![屏幕快照 2021-08-07 下午5.17.39](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-07 下午5.17.39.png)

下面解释这个算法的每一步的作用。

第3步：此时的 $\tilde{r}_k$ 是当前的一个小波系数的估计。计算和测量值之间的误差 $z_k$

第4步：减小 forward model 的误差。通常的做法是计算 $||y - M_{\Omega}Fx||^2$ 的梯度步，这里的做法则是考虑了采样的概率 $E_{\Omega}\| y - M_{\Omega} Fx\|^2$。这一步的目标是白化 effective noise。

第5步：估计小波域上的方差。这是 AMP 算法里比较重要的一个步骤。

第6、7、8步：比较当前的误差level 和此前的误差level，如果变得更大了，就停止算法，否则就继续。误差变得更大就说明没有必要继续迭代了。

第9步：计算去噪后的小波系数。注意，这里的去噪声是在图像域进行的。而且 denoiser 包含了估计的噪声强度作为输入，或者说denoiser 取决于噪声的强度（这一点感觉有点像 score matching，某种意义上说，这是否就可以看成是一种往概率更高的地方移动？）。gamma 是一个可调的参数。

第10步：计算 MC 估计，这里计算的是关于小波的一个平均梯度？不太懂是什么含义。

第11步：更新小波系数，只不过这里每个小波 band 的更新是不同的。叫做 colored Onsager correction

最后的第 13 步就是输出最后结果。

### Calculating Average Partial Derivatives

这是对第 11 步的详细解释。这一步是VDMAP算法中很关键的一步。根据论文的写法，这个估计分别对实数部分和虚数部分进行。
$$
\langle\partial \mathcal{R}(g(\mathbf{r}))\rangle_{\text {sband }} \approx \frac{1}{J_{s}} \mathcal{R}\left(\mathbf{b}_{s}^{t}\right)\left(\frac{\mathcal{R}\left(g\left(\mathbf{r}+\eta \mathcal{R}\left(\mathbf{b}_{s}\right)\right)-g(\mathbf{r})\right)}{\eta}\right)
$$
之所以是 MC 估计，是因为它并没有真的计算，而是通过一个小量来估计。上面的公式仅仅给出了实部的估计，虚部是类似的。这里的 R 表示取实部。$b_s$ 是一个复数高斯分布的采样。方差是 2（因为实部和虚部各为 1），$\eta$ 是一个小量，作者取 $\frac{|max(r)|}{1000}$.

The effective noise within each wavelet subband, s, follows a white Gaussian distribution with a variance equal to $τ_{k,s}$ from line 5. 这是这篇文章的一个假设？但是我不知道这个怎么证明，因为在实验中，作者用的是 variable density sampling，并不是 caartesian sampling。

### Removing Colored Noise

用的网络是 DnCNN，似乎这篇文章的改进就是像算法中写的那样，把 $\tau_k$ 也作为网络的输入了。因为使用了小波，增加了很多个 tensor（但是我不知道为什么 size 都是一样的）

训练似乎就是不同的 wavelet band 用不同的噪声，然后去噪训练。



## 实验

用的数据集是来自 mridata.org，作者训练的时候用的是 patch。

用的数据集假设都是实值图像。

使用 variable density sampling 策略。

比较了传统的 VDAMP，使用不同的去噪器，以及其他的 P&P 方法。但是没有比较 ADMM-net 这样的方法，因为作者表示 ADMM-net 方法没有泛化性，不能对其他的 sampling 策略有效果（这个理由有点牵强了）

在讨论部分，作者说因为 VDAMP 最后一步计算了 varaince map，所以可以利用 Stein's unbiased risk 估计不确定性。但是由于 AMP 方法本身算的应该是 MAP，所以我认为这里的不确定性仅仅是 MAP 的不确定性（可以理解成在一定的小范围都差不多是 MAP），和真正的后验分布估计不一样。



## 参考文献

Christopher A. Metzler, Ali Mousavi, and Richard Baraniuk, “Learned d-amp: Principled neural network based compressive image recovery,” in Advances in Neural Information Processing Systems, 2017, pp. 1772–1783. 这篇是深度学习的 Plug & Play 的文章

Rizwan Ahmad, Charles A Bouman, Gregery T Buzzard, Stanley Chan, Sizhou Liu, Edward T Reehorst, and Philip Schniter, “Plug and play methods for magnetic resonance imaging (long version),” arXiv preprint arXiv:1903.08616, 2019. Plug & Play 用于MRI 的 review 文章。

Charles Millard, Aaron T Hess, Boris Mailh´e, and Jared Tanner, “Approximate message passing with a colored aliasing model for variable density fourier sampled images,” arXiv preprint arXiv:2003.02701, 2020. 本文是基于这篇文章的。