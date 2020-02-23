### Paper:

Learning-based Optimization of the Under-sampling Pattern in MRI

### Author:

Cagla Deniz Bahadir, Adrian V. Dalca, and Mert R. Sabuncu

### Year:

2019

### Notes:

阅读时间：2020.02.01

泛读。这篇文章研究的是 MRI 的重建，作者关注的是如何同时训练 sampling sheme 和重建网络。关于重建网络，这篇文章比较简单，用的 U-net 结构，而训练 sampling scheme，作者提出的方法是用伯努利分布来建模，表示kspace 的该位置被采样的概率。利用 sigmoid 函数来近似表示 0-1 二值，伯努利分布的参数是可学习的参数，因为是概率分布，所以实际的训练样本的 mask 使用随机的均匀分布采样结果和伯努利分布参数结合，通过sigmoid计算得到一个近似0-1的值，作为 mask 和 full kspace 相乘。这样可以进行端到端的训练。从实验效果上看，感觉没多少提升。单纯地训练 kspace 的  sampling scheme 并不能真正有效地解决重建问题。

设 $p_i$ 表示 sampling scheme mask 的伯努利分布的概率参数，$m^{(k)}$ 表示该概率分布下随机采样的 0-1 二值结果，$A$ 表示重建网络。那么该网络训练的就是如下的损失函数，第一项表示希望sampling的数量尽可能少，第二项表示通过 K 次随机采样的 mask 的平均重建效果

<img src="http://latex.codecogs.com/svg.latex? \arg \min _{\boldsymbol{p}, \boldsymbol{A}} \lambda \sum_{i} \boldsymbol{p}_{i}+\sum_{j} \frac{1}{K} \sum_{k=1}^{K}\left\|A\left(F^{H} \operatorname{diag}\left(\boldsymbol{m}^{(k)}\right) F \boldsymbol{x}_{j}\right)-\boldsymbol{x}_{j}\right\|_{1}" border="0"/>

考虑了sigmoid近似后，得到：

<img src="http://latex.codecogs.com/svg.latex? \arg \min _{\boldsymbol{p}, \theta} \lambda \sum_{i} \boldsymbol{p}_{i}+\sum_{j} \frac{1}{K} \sum_{k=1}^{K}\left\|A_{\theta}\left(F^{H} \operatorname{diag}\left(\sigma_{s}\left(\boldsymbol{u}^{(k)}-\boldsymbol{p}\right)\right) F \boldsymbol{x}_{j}\right)-\boldsymbol{x}_{j}\right\|_{1}" border="0"/>

$u^{(k)}$ 表示 $U(0,1)$，$\sigma_s$ 表示 sigmoid 函数

网络结构（下图的 weight parameters 应该指的是伯努利的参数，但感觉有一点奇怪）：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-01%E4%B8%8B%E5%8D%8812.48.01.png"/>

### Bibtex:

```latex
@inproceedings{bahadir2019learning,
  title={Learning-based optimization of the under-sampling pattern in MRI},
  author={Bahadir, Cagla Deniz and Dalca, Adrian V and Sabuncu, Mert R},
  booktitle={International Conference on Information Processing in Medical Imaging},
  pages={780--792},
  year={2019},
  organization={Springer}
}
```

