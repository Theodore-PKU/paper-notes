### Paper:

Inverse GANs for Accelerated MRI Reconstruction

### Author:

Dominik Narnhofer, Kerstin Hammernik, Florian Knoll, and Thomas Pock

### Year:

2019

### Notes:

阅读日期：2020.02.20

精读。这篇文章读得比较仔细。研究的是 MRI 重建，实验用的 fastMRI 的多线圈数据。作者的方法概括起来其实很简单：1. 训练 GAN；2. Bora 方法优化 latent variable；3. DIP 方法同时优化 z 和网络参数。从实验结果上看，这种方法细节生成的非常好，但是指标上不如作者自己提出的 VN，而且 VN 的效果感觉已经非常不错了。因为用的是多线圈，作者的处理手段就是用传统的 ESPIRiT 算法来估计 sensitivity map，这样就可以直接用到测量矩阵中。网络的结构好像没什么特别的，就是单纯的 CNN，不知道是否在这上面改进效果会更好。为了处理复数，使用的是双通道，生成器的最后一层是 tanh，作者乘了1.6的系数（这个作用可能是为了训练效果，避免 tanh 取 1 时的困难）。图像做了预处理，除以 kspace 中间区域的最大值 magnitude。

GAN 结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200220134247.png"/>

第一步优化：

<img src="http://latex.codecogs.com/svg.latex? \bar{z}=\underset{z \in \mathbb{R}^{d}}{\operatorname{argmin}} \frac{1}{2}\left\|A G_{\theta}(z)-\hat{f}\right\|^{2}, \quad \text { s.t. }\|z\| \leq \sqrt{d}" border="0"/>

第二步优化：

<img src="http://latex.codecogs.com/svg.latex? \left(z^{*}, \theta^{*}\right)=\underset{(z, \theta) \in \mathbb{R}^{d} \times \mathbb{R}^{l}}{\operatorname{argmin}} \frac{1}{2}\left\|A G_{\theta}(z)-\hat{f}\right\|^{2}, \quad \text { s.t. }\|z\| \leq \sqrt{d}" border="0"/>

算法是：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200220134344.png"/>

实验的效果，细节生成地很不错，但是指标不如 VN：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200220134508.png"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200220134602.png"/>

### Bibtex:

```latex
@inproceedings{narnhofer2019inverse,
  title={Inverse GANs for accelerated MRI reconstruction},
  author={Narnhofer, Dominik and Hammernik, Kerstin and Knoll, Florian and Pock, Thomas},
  booktitle={Wavelets and Sparsity XVIII},
  volume={11138},
  pages={111381A},
  year={2019},
  organization={International Society for Optics and Photonics}
}
```

