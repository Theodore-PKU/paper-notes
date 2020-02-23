### Paper:

Deep Bayesian Inversion

### Author:

Jonas Adler, Ozan Öktem

### Year:

2018

### Notes:

阅读日期：2020.02.11

泛读。这篇文章说是要使用贝叶斯的思路求解逆问题，但实际上的做法非常简单。作者考虑了两种做法，一种是 conditional GAN，并且包含一个随机分布的输入；一种做法是直接训练两个网络，一个负责生成后验均值，一个负责计算后验方差。第一种做法可以理解用 GAN 直接计算后验分布。第二种做法就非常暴力了，特别是关于方差的部分非常没有道理。总体来说我觉得这种做法没有真正用上了贝叶斯的思路，只是套了一个外壳。另外文章没怎么说网络结构，只说是 multi-scale residual network。

第一种做法：

<img src="http://latex.codecogs.com/svg.latex? \theta^{*} \in \underset{\theta \in \Theta}{\arg \min }\left\{\sup _{\phi \in \Phi} \mathbb{E}_{(x, y) \sim \mu}\left[\mathrm{D}_{\phi}(\mathrm{x}, \mathrm{y})-\mathbb{E}_{\mathrm{z} \sim \eta}\left[\mathrm{D}_{\phi}\left(\mathrm{G}_{\theta}(\mathrm{z}, \mathrm{y}), \mathrm{y}\right)\right]\right\}\right." border="0"/>

> 这里的判别器 D 就相当于是判断生成的 x 和 y 的联合分布和原始的联合分布是一样的。但是仅仅是均值接近而已，因为现实中，对于一个 y，我们只有一个 x 是已知的，而不是多个 x，换句话说这个分布很尖锐。
>
> 感觉这种 GAN 的机理有些怪怪的，判别器到底实现的功能是什么？实际中，对于 y 只有一个 x 对应，但是 GAN 生成的是多个 x 和一个 y。而 D 要判断的是这些 x～y 是否和原始的 x~y 是否一样，sup 是希望可以分开，或者说两者差异越大越好，而 min 是让 D 无法区分开，那么给人的感觉 GAN 生成的也说不上就是后验分布。

第二种做法：

<img src="http://latex.codecogs.com/svg.latex? \theta^{*} \in \underset{\theta}{\arg \min }\left\{\mathbb{E}_{(x, y) \sim \mu}\left[\left\|x-\mathcal{T}_{\theta}^{\dagger}(y)\right\|_{X}^{2}\right]\right\}" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \phi^{*} \in \underset{\phi}{\arg \min }\left\{\mathbb{E}_{(x, y) \sim \mu}\left[\left\|\mathrm{h}_{\phi}(\mathrm{y})-\left(\mathrm{x}-\mathcal{T}_{\theta^{*}}^{\dagger}(\mathrm{y})\right)^{2}\right\|_{X}^{2}\right]\right\}" border="0"/>

### Bibtex:

```latex
@article{adler2018deep,
  title={Deep bayesian inversion},
  author={Adler, Jonas and {\"O}ktem, Ozan},
  journal={arXiv preprint arXiv:1811.05910},
  year={2018}
}
```

