### Paper:

Compressed Sensing MRI Reconstruction using a Generative Adversarial Network with a Cyclic Loss

### Author:

Tran Minh Quan, Thanh Nguyen-Duc, and Won-Ki Jeong

### Year:

2018

### Notes:

精读。这篇文章提出的方法是用一个GAN重建MRI，创新点（可能这是第一篇使用）在于不仅是用了对抗loss，还包含了 kspace loss 和图像域的 loss。作者提出的网络结构似乎也有一些和其他人的做法不太一样的地方。概括起来就是，一个类似 Unet，但是内部包含了残差连接的网络结构，重复两次（或多次）构成一个堆叠的生成网络，判别网络使用了生成网络的 Unet 中的相同的编码器结构，结合了对抗 loss，kspace loss，image loss 的GAN重建方法。

作者的思路基于一般的逆问题中GAN的优异表现，因此认为用于MRI重建也是合适的。

> 一定有一种统一的框架可以适用于所有的逆问题。

整体框架：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-31%E4%B8%8B%E5%8D%884.50.44.png" style="zoom:67%;" />

关于网络的训练，在图上看起来作者生成fake图像有两种类型，但没有具体说明两者有何差异。第一个 measurement 似乎也还是从数据集中获得的。虽然如果假设只有measurement，也说的通。

网络结构：

生成器部分是一个Unet的结构（也可以说是自编码结构，但是这里的多尺度信息没有使用拼接构成输入）。每一个分辨率block中使用了残差结构，下采样部分使用的 stride = 2 的卷积层（上采样则是 stride = 2 的 deconvolution 层）。在 Encoder block 和 Decoder block 中的残差结构中，通道数会在 conv_m 层减半，最后再恢复。最终生成器的输出是负的伪影，因此需要再加上原始输入。

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-31%E4%B8%8B%E5%8D%884.56.57.png)

如果使用多个生成网络，就可以构成作者所谓的 refine 的网络（但作者没有说在这种情况下的损失函数是否也对中间值（checkpoint）有效）。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-31%E4%B8%8B%E5%8D%884.57.04.png" style="zoom:33%;" />

判别器的大部分内容和生成器的前半部分是一样的，只是最后加上了判别的层。

> 从理论上说判别器和生成器的编码部分确实有某种相似之处，是不是可以尝试一些东西。
>
> 另外残差结构的降低通道数的做法在某种程度上可以视作一种低秩分解？这个结构如何和压缩感知或深度网络表征图像流形是否有关系？是否可以结合？这算一个技巧了吧。

损失函数：$\mathbf{d}$ 表示某种距离度量，可以是1范数，也可以是2范数。另外这里的loss可以用不同的样本来计算。
$$
L_{a d v}(G, D)=\underset{m \in M}{E}\left[1-\log D\left(G\left(s_{0}\right)\right)\right]+\underset{s \in S}{E}[\log D(s)]\\
\begin{aligned}
L_{c y c}(G) &=L_{\text {freq}}(G)+L_{\text {imag}}(G) \\
&=\mathbf{d}(m[i], \bar{m}[i])+\mathbf{d}(s[j], \bar{s}[j])
\end{aligned}
$$
<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-31%E4%B8%8B%E5%8D%884.55.15.png" style="zoom: 33%;" />

总的损失函数：
$$
L_{\text {total}}=L_{a d v}(G, D)+\alpha L_{\text {freq}}(G)+\gamma L_{\text {imag}}(G)
$$
训练细节：使用的复数表示方法是两通道。判别器训练好之后的结果应该是50%的成功率。$\alpha=1,\gamma = 10$，ADAM 优化器，初始学习率为 $1e^-4$，每500个epoch下降（没说怎么下降）。采样方式主要是径向采样，也有实验比较了不同的采样方式的不同。

实验内容：

1. 数据集 IXI database（脑MRI），Data Science Bowl challenge（胸MRI）。数据网址分别是http://brain-development.org/ixi-dataset/ 和 https://www.kaggle.com/c/second-annual-data-science-bowl/data
2. 不同的采样率（10%，20%，30%，40%）下和其他方法的对比。
3. 数据预处理提到了 range normalization，但是对于实值的MRI图像如何处理成复数似乎没讲清楚。
4. 比较了运行时间和图像质量（PSNR，SSIM，NRMSE）
5. 比较了论文提出的方法对于不同的采样方式的表现。

从结果上看，作者提供的方法都优于其他方法。对于不同的采样方式，效果差别不是很大。

> 思考：作者提到了字典学习，我想到的是，其实也可以把深度学习的训练过程看成是一个训练字典的过程，只不过深度学习在框架的角度上有一些区别，如果是训练的字典，原来的方法还要在新的sample 下计算稀疏系数，但是深度学习直接就计算好了，如果把深度学习看成编码过程也直接学习了，似乎也解释的通。那么字典学习中的技巧（patch、non local 等）都可以在深度学习中得到应用。

### Bibtex:

```latex
@article{quan2018compressed,
  title={Compressed sensing MRI reconstruction using a generative adversarial network with a cyclic loss},
  author={Quan, Tran Minh and Nguyen-Duc, Thanh and Jeong, Won-Ki},
  journal={IEEE transactions on medical imaging},
  volume={37},
  number={6},
  pages={1488--1497},
  year={2018},
  publisher={IEEE}
}
```