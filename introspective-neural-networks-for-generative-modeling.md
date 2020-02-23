### Paper:

Introspective Neural Networks for Generative Modeling

### Author:

Justin Lazarow, Long Jin, Zhuowen Tu

### Year:

2017

### Notes:

这篇文章的主要想法是将生成器和判别器用一个卷积网络表示。也就是说用一个分类的判别网络作为生成模型。解决的是一个无监督的生成问题。给定一组图片，如何生成和图片分布相近的图片。

考虑的是patch，所以可以认为只有一张图片训练也是可以的。但是生成的结果就类似纹理。事实上，作者训练的图像都是纹理。

作者提出的方法称为 introspective neural networks generative modeling.

首先考虑正样本和负样本，分别对应真实图片和生成的图片。针对这两类图片，分别有label +1, -1，利用贝叶斯公式，可以写出下式：
$$
p(\mathbf{x} | y=+1)=\frac{p(y=+1 | \mathbf{x}) p(y=-1)}{p(y=-1 | \mathbf{x}) p(y=+1)} p(\mathbf{x} | y=-1)
$$
假设正样本和负样本的数量一样多，可以化简为：
$$
p(\mathbf{x} | y=+1)=\frac{p(y=+1 | \mathbf{x})}{1-p(y=+1 | \mathbf{x})} p(\mathbf{x} | y=-1)
$$
根据 The generative via discriminative learning framework (GDL)，可以通过迭代来逼近 $p(\mathbf{x} | y=+1)$，这也就是真实图像的分布。记 $p^-(\mathbf{x}) = p(\mathbf{x}|y=-1)$，迭代方式如下：
$$
\begin{array}{c}{p_{0}^{-}(\mathbf{x})=U(\mathbf{x})} \\ {p_{t}^{-}(\mathbf{x})=\dfrac{1}{Z_{t}} \dfrac{q_{t}(y=+1 | \mathbf{x})}{q_{t}(y=-1 | \mathbf{x})} \cdot p_{t-1}^{-}(\mathbf{x}), t=1 ,..., T}\end{array}
$$
$U(\mathbf{x})$ 是正态分布，$Z_t$ 是归一化常数。

实际训练过程：

1. $S_{+}=\left\{\left(\mathbf{x}_{i}, y_{i}=+1\right) | i=1,. . ., n\right\}$ 是正样本集合。$S_{-}^{0}=\left\{\left(\mathbf{x}_{i},-1\right) | i=1, \ldots, l\right\}$ 是初始的负样本集合。

2. 对 $S_+$ 和 $S_{-}^t$ 训练一个分类器 $C^t$. 设网络参数为 $\mathrm{W}_{t}=\left(\mathbf{w}_{t}^{(0)}, \mathbf{w}_{t}^{(1)}\right)$，$\mathbf{w}_t^{(0)}$ 表示除了最后一层的参数，$\mathbf{w}_t^{(1)}$ 表示最后一层参数，用sigmoid函数作为判别概率，则有
   $$
   q_{t}\left(y | \mathbf{x} ; \mathbf{W}_{t}\right)=1 /\left(1+\exp \left\{-y<\mathbf{w}_{t}^{(1)}, \phi\left(\mathbf{x} ; \mathbf{w}_{t}^{(0)}\right)>\right\}\right)
   $$
    损失函数是交叉熵。

3. 下面一步是更新概率分布 $p_t^{-}(\mathbf{x})$ 和抽样，两步合为一步，具体的实现是对 $\mathbf{x}$ 进行更新。根据 GDL 的迭代，
   $$
   p_{t}^{-}(\mathbf{x})=\prod_{a=1}^{t} \frac{1}{Z_{a}} \frac{q_{a}\left(y=+1 | \mathbf{x} ; \mathbf{W}_{a}\right)}{q_{a}\left(y=-1 | \mathbf{x} ; \mathbf{W}_{a}\right)} p_{0}^{-}(\mathbf{x})\\
   g_{a}(\mathbf{x})=\frac{q_{a}\left(y=+1 | \mathbf{x} ; \mathbf{W}_{a}\right)}{q_{a}\left(y=-1 | \mathbf{x} ; W_{a}\right)}=\exp \left\{<\mathbf{w}_{a}^{(1)}, \phi\left(\mathbf{x} ; \mathbf{w}_{a}^{(0)}\right)>\right\}\\
   \ln g_{a}(\mathbf{x})=<\mathbf{w}_{a}^{(1)}, \phi\left(\mathbf{x} ; \mathbf{w}_{a}^{(0)}\right)>
   $$
   又利用 Langevin dynamics，$\Delta \mathbf{x}=\nabla\left(\sum_{a=1}^{t} \ln g_{a}(\mathbf{x})\right)+\eta$，$\eta$ 是正态分布。为了减少计算，直接从 $t-1$ 步的分布开始，对 $p_{t-1}^{-}(\mathbf{x})$ 的负样本，也就是前一次生成的负样本进行梯度更新，计算的梯度目标是
   $$
    \ln g_{t}(\mathbf{x})=<\mathbf{w}_{t}^{(1)}, \phi\left(\mathbf{x} ; \mathbf{w}_{t}^{(0)}\right)>
   $$
    

4. 经过 T 的训练，得到 T 个分类器。

5. 生成图像时，从初始分布 $U(\mathbf{x})$ 出发，从 $t = 1$ 开始，由每一个分类器的网络更新 $\mathbf{x}$，直到该分类器的判别结果处于临界值。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-11%E4%B8%8B%E5%8D%884.56.36.png" style="zoom: 67%;" />

几个注意事项：

1. 因为用patch训练生成，对于重叠的部分，计算loss的时候要取平均
2. 训练分类器时通过保留一些更早生成的样本使训练更稳定
3. 可以只保留一部分的分类器，比如每隔b个分类器取一个。

作者和GAN进行了比较，但是没有什么特别的见解。

### Bibtex:

```latex
@inproceedings{lazarow2017introspective,
  title={Introspective neural networks for generative modeling},
  author={Lazarow, Justin and Jin, Long and Tu, Zhuowen},
  booktitle={Proceedings of the IEEE International Conference on Computer Vision},
  pages={2774--2783},
  year={2017}
}
```

