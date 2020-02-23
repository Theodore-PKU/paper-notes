### Paper:

Noise2Noise: Learning Image Restoration without Clean Data

### Author:

Jaakko Lehtinen, Jacob Munkberg, Jon Hasselgren, Samuli Laine, Tero Karras, Miika Aittala, Timo Aila

### Year:

2018

### Notes:

阅读日期：2020.01.15

泛读。这篇文章研究的是在没有 full-sample 或高质量 target 的情况下如何训练。作者的想法来自统计上的，如果target 的均值保持不变，即使带有误差，在一定的损失函数下，依然可以得到相同的统计结果。也就是说，u作者将<img src="http://latex.codecogs.com/svg.latex? \underset{\theta}{\operatorname{argmin}} \mathbb{E}_{x}\left\{\mathbb{E}_{y | x}\left\{L\left(f_{\theta}(x), y\right)\right\}\right\}" border="0"/> 替换为了<img src="http://latex.codecogs.com/svg.latex? \underset{\theta}{\operatorname{argmin}} \sum_{i} L\left(f_{\theta}\left(\hat{x}_{i}\right), \hat{y}_{i}\right)" border="0"/>。所以当不具有真实的target data 时，用其他的但是均值不变的 data，极小化损失函数可以达到一样的效果，这样就可以考虑用 noise data 来替代 target。（但是每个target 应该需要多个 noise data 来替代才可以？）

作者把这种方法应用的MRI上，也就是训练时target不是groudtruth，而是其他的的欠采样的图像（应该是同一个slice）。考虑 data fidelity，损失函数设计时，先还原为 kspace，然后填补未采样的区域，再转回图像域，损失函数如下：

<img src="http://latex.codecogs.com/svg.latex? \left(\mathcal{F}^{-1}\left(R_{\hat{x}}\left(\mathcal{F}\left(f_{\theta}(\hat{x})\right)\right)\right)^{1}-\hat{y}\right)^{2}" border="0"/>

### Bibtex:

```latex
@article{lehtinen2018noise2noise,
  title={Noise2noise: Learning image restoration without clean data},
  author={Lehtinen, Jaakko and Munkberg, Jacob and Hasselgren, Jon and Laine, Samuli and Karras, Tero and Aittala, Miika and Aila, Timo},
  journal={arXiv preprint arXiv:1803.04189},
  year={2018}
}
```

