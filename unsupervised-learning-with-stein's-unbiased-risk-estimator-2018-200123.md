### Paper:

Unsupervised Learning with Stein’s Unbiased Risk Estimator

### Author:

Christopher A. Metzler, Ali Mousavi, Reinhard Heckel

### Year:

2018

### Notes:

阅读时间：2020.01.23

泛读。这篇文章研究的是当没有 clean image 作为 groudtruth 时，使用 noisy image 或 noisy measurement 直接训练网络。因为没有 label，所以是用 SURE loss 来训练。这篇文章的主要内容就是将 SURE loss 用于网络的训练。SURE loss 很早就提出来了。在这篇文章中，作者讨论了两个情境，一个是去噪，一个压缩感知图像恢复。从实验结果上看，效果还是很不错的。不过作者并没有分析，为什么 SURE loss 在压缩感知问题上也是有效的，这一点比较让人诧异。

先介绍 SURE 以及 general SURE（GSURE），然后介绍两个任务下的具体做法。设网络为 $f_{\theta}$，$y = x + w, w\sim N(0, \sigma^2\mathbf{I})$，SURE 定义为：

<img src="http://latex.codecogs.com/svg.latex? \mathbb{E}_{\mathbf{w}}\left[\frac{1}{n}\left\|\mathbf{x}-f_{\theta}(\mathbf{y})\right\|^{2}\right]=\mathbb{E}_{\mathbf{w}}\left[\frac{1}{n}\left\|\mathbf{y}-f_{\theta}(\mathbf{y})\right\|^{2}\right]-\sigma_{w}^{2}+\frac{2 \sigma_{w}^{2}}{n} \operatorname{div}_{\mathbf{y}}\left(f_{\theta}(\mathbf{y})\right)" border="0"/>

其中，

<img src="http://latex.codecogs.com/svg.latex? \operatorname{div}_{\mathbf{y}}\left(f_{\theta}(\mathbf{y})\right)=\sum_{n=1}^{N} \frac{\partial f_{\theta n}(\mathbf{y})}{\partial y_{n}}" border="0"/>

这一项的计算，作者使用了一个近似：

<img src="http://latex.codecogs.com/svg.latex? \operatorname{div}_{\mathbf{y}}\left(f_{\theta}(\mathbf{y})\right)=\lim _{\epsilon \rightarrow 0} \mathbb{E}_{\mathbf{b}}\left\{\mathbf{b}^{t}\left(\frac{f_{\theta}(\mathbf{y}+\epsilon \mathbf{b})-f_{\theta}(\mathbf{y})}{\epsilon}\right)\right\}" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \operatorname{div}_{\mathbf{y}}\left(f_{\theta}(\mathbf{y})\right) \approx \mathbf{b}^{t}\left(\frac{f_{\theta}(\mathbf{y}+\epsilon \mathbf{b})-f_{\theta}(\mathbf{y})}{\epsilon}\right)" border="0"/>

对于一般情形，我们得到的是 noisy measurement，也就是 $y = Hx + w, H \neq I$，这时有一个 GSURE，

<img src="http://latex.codecogs.com/svg.latex? \mathbb{E}_{\mathbf{w}}\left[\frac{1}{n}\left\|\mathbf{P} \mathbf{x}-\mathbf{P} f_{\theta}(\mathbf{y})\right\|^{2}\right]=\mathbb{E}_{\mathbf{w}}\left[\frac{1}{n}\|\mathbf{P} \mathbf{x}\|^{2}+\frac{1}{n}\left\|\mathbf{P} f_{\theta}(\mathbf{y})\right\|^{2}+\frac{2 \sigma_{\mathbf{w}}^{2}}{n} \operatorname{div}_{\mathbf{y}}\left(f_{\theta}(\mathbf{y})\right)-\frac{2}{n} f_{\theta}(\mathbf{y})^{t} \mathbf{H}^{\dagger} \mathbf{y}\right]" border="0"/>

其中，$\mathbf{P}$ 表示 $\mathbf{H}$ 的对应空间正交投影算子。

在实验方面，作者首先研究了 SURE 用于 DIP 的去噪任务，和 MSE 相比，这种 loss 不需要提前终止就能得到很好的结果。在训练的去噪问题上，作者使用 DnCNN 的网络结构。最终效果只比有 groundtruth 的 MSE loss 训练的效果差一点点。

在压缩感知任务中，作者使用的感知矩阵时随机高斯矩阵，采样率为20%，使用的方法是 LDAMP（这篇以前看过的）。由于压缩感知任务，有测量矩阵，因此可以使用 GSURE，但是在实验中，作者也使用了 SURE，有两种结果：

<img src="http://latex.codecogs.com/svg.latex? \theta_{\mathrm{SURE}}=\arg \min _{\theta} \sum_{\ell=1}^{L} \frac{1}{n}\left\|\mathbf{y}_{\ell}-f_{\theta}\left(\mathbf{y}_{\ell}\right)\right\|^{2}-\sigma^{k^{2}}+\frac{2\left(\sigma^{k}\right)^{2}}{n} \operatorname{div}_{\mathbf{y}_{\ell}}\left(f_{\theta}\left(\mathbf{y}_{\ell}\right)\right)" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \theta_{\mathrm{GSURE}}=\arg \min _{\theta} \sum_{\ell=1}^{L} \frac{1}{n}\left\|\mathbf{P} f_{\theta}\left(\mathbf{y}_{\ell}\right)\right\|^{2}+\frac{2 \sigma_{\mathbf{w}}^{2}}{n} \operatorname{div}_{\mathbf{y}}\left(f_{\theta}\left(\mathbf{y}_{\ell}\right)\right)-\frac{2}{n} f_{\theta}\left(\mathbf{y}_{\ell}\right)^{t} \mathbf{H}^{\dagger} \mathbf{y}_{\ell}" border="0"/>

比较无法理解的地方在于 $f_{\theta}(y_l)$ 的结果是什么，是重建图像的测量值吗？这一点在论文中没有具体说明。

### Bibtex:

```latex
@article{metzler2018unsupervised,
  title={Unsupervised Learning with Stein's Unbiased Risk Estimator},
  author={Metzler, Christopher A and Mousavi, Ali and Heckel, Reinhard and Baraniuk, Richard G},
  journal={arXiv preprint arXiv:1805.10531},
  year={2018}
}
```

