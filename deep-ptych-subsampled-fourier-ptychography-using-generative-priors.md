### Paper:

Deep Ptych: Subsampled Fourier Ptychography using Generative Priors

### Author:

Fahad Shamshad, Farwa Abbas, Ali Ahmed

### Year:

2018

### Notes:

泛读。这篇文章解决的问题不是一个逆问题，但是和逆问题非常相似。作者的方法基于 [Compressed Sensing using Generative Models](compressed-sensing-using-generative-models.md) 这篇文章的做法，即通过训练好的生成模型 GAN 或 VAE 来重建信号。作者在 [Compressed Sensing using Generative Models](compressed-sensing-using-generative-models.md) 提出的算法框架的基础上，稍微改进了一下目标函数。实验结果表明，当采样率非常低的时候，这种基于生成模型的效果非常好。

问题描述：Fourier Ptychography Problem，这个问题大概讲的是远程摄像时，成像系统的问题，获得的图像分辨率较低，人们希望从多张图片中重建出高清的图片。数学形式为：
$$
\boldsymbol{y}_{\ell}=\sum_{\ell=1}^{L}\left|\mathcal{A}_{\ell}(\boldsymbol{x})\right|+\boldsymbol{n}_{\ell}
$$
其中 $\mathcal{A}_{\ell}=\mathcal{M}_{\ell} \mathcal{F}^{-1} \mathcal{P}_{\ell} \circ \mathcal{F}$，$\mathcal{F}$ 表示傅立叶变换，$\mathcal{P}_l$ 表示频率域的滤波器，$\mathcal{M}_l$ 表示空间域的随机采样。出了有一个非线性的模长运算，其他和一般的逆问题很相似。

作者使用的求解方法是，假设有一个训练好的生成模型 $G$，优化目标函数（这个方法作者命名为 deep ptych）：
$$
\hat{\boldsymbol{z}}=\underset{\boldsymbol{z} \in \mathbb{R}^{k}}{\operatorname{argmin}} \sum_{\ell=1}^{L}\left\|\boldsymbol{y}_{\ell}-| \mathcal{A}_{\ell}(G(\boldsymbol{z}))|\right\|_{2}^{2}
$$
整体框架如下：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-02%E4%B8%8B%E5%8D%889.38.08.png" style="zoom:50%;" />

作者提出的改进的目标函数是（命名为 deep ptych +）：
$$
(\hat{\boldsymbol{x}}, \hat{\boldsymbol{z}})=\underset{\boldsymbol{z} \in \mathbb{R}^{k}, \boldsymbol{x} \in \mathbb{R}^{n}}{\operatorname{argmin}} \sum_{\ell=1}^{L}\left\|\boldsymbol{y}_{\ell}-| \mathcal{A}_{\ell}(\boldsymbol{x})|\right\|_{2}^{2}+\lambda\|\boldsymbol{x}-G(\boldsymbol{z})\|_{2}^{2}
$$
实验（MNIST，CelebA）结果中，有趣的是在有噪声的情况下，deep ptych 表现得比 deep ptych + 好，而deep ptych + 在没有噪声的情况下表现得更好。

> 思考：在这两个计算框架下，都得保证不会出现两个 $z$ （对应两个 $x$） 都满足第一项，也就是不存在两个 $z$，$G(z) $之差是 $\mathcal{A}_{\ell}(G(\boldsymbol{z}))$ 的零空间。只不过第二个计算框架认为 $G(z)$ 只是一种近似不要求重建的信号一定在 $G(z)$ 的空间中。
>
> 看这篇文章的时候，想到的是，可以从生成模型生成的 低维流形上的零空间来考虑。也许有和压缩感知框架对应的理论分析，比如唯一解。压缩感知因为有1范数极小，所以似乎不用太在意零空间。可以从解的存在性和选择这个角度思考

### Bibtex:

```latex
@inproceedings{shamshad2019deep,
  title={Deep ptych: Subsampled fourier ptychography using generative priors},
  author={Shamshad, Fahad and Abbas, Farwa and Ahmed, Ali},
  booktitle={ICASSP 2019-2019 IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP)},
  pages={7720--7724},
  year={2019},
  organization={IEEE}
}
```