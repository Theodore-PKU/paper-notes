### Paper:

SANTIS: Sampling-Augmented Neural neTwork with Incoherent Structure for MR image reconstruction

### Author:

Fang Liu, Lihua Chen, Richard Kijowski, Li Feng

### Year:

2019

### Notes:

阅读日期：2020.02.29

泛读。这篇文章研究的是 MRI 的重建，使用的方法是 cyclic loss GAN。这个方法是别人提出来过的，作者只是用来当做重建算法。作者的创新在于训练方式而不是重建算法。作者认为如果训练的时候固定 sampling scheme，会使得网络的鲁棒性不够，再测试时，如果 sampling scheme 发生改变，效果就会变差。作者提出的解决办法也很简单，就是对每个训练样本，每次训练时都使用不同的 sampling scheme（估计加速程度一样，只是重新随机采样了）。作者的实验也是对比固定 sampling scheme和可变sampling scheme 的差别。重建算法方便，GAN 框架，loss 还有 zero-fill loss（也就是所谓的 cyclic loss，目的在于kspace 的一致，也就是 data consistency）和一般的 image loss。生成器用的是有 residual learning 的 U-Net。

Cyclic loss:

![](http://latex.codecogs.com/svg.latex? \lambda_{\operatorname{los} s 2} \mathbb{E}_{x_{u} \rightarrow P\left(x_{u}\right)}\left[\left\|\Phi_{u} F\left(x_{u}\right)-x_{u}\right\|_{N}\right]+\lambda_{l o s s 1} \mathbb{E}_{x_{u} \rightarrow P\left(x_{u}\right)}\left[\left\|F\left(x_{u}\right)-x\right\|_{N}\right])

网络结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200301125808.png" width="80%"/>

### Bibtex:

```latex
@article{liu2019santis,
  title={SANTIS: Sampling-augmented neural network with incoherent structure for MR image reconstruction},
  author={Liu, Fang and Samsonov, Alexey and Chen, Lihua and Kijowski, Richard and Feng, Li},
  journal={Magnetic resonance in medicine},
  volume={82},
  number={5},
  pages={1890--1904},
  year={2019},
  publisher={Wiley Online Library}
}
```

