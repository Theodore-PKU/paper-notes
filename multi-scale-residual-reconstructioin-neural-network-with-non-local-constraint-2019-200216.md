### Paper:

Multi-Scale Residual Reconstruction Neural Network With Non-Local Constraint

### Author:

Wan Li, Fang Liu, Licheng Jiao, Fei Hu

### Year:

2019

### Notes:

阅读日期：2020.02.16

精读。这篇文章研究的是 CS 重建。作者基于 patch（non-overlapping），所以网络的输入图像是 32 x 32 大小。作者的主要创新点有两个，一个是使用了 non-local 操作，一个是采用了 multi-scale 的结构。non-local 操作指的是对特征图先计算一个相互关系的系数，然后用这个系数作为权重融合全局特征图的信息得到该像素点的特征图信息。multi-scale 结构比较简单，使用了三个不同代表不同 scale 的子网络，具体而言，这些子网络的区别在于使用的卷积层的大小不同。子网络的结构是一个 residual block 结构，每个 block 则是卷积 + non-local + 卷积。另外，在 non-local 中，也有一个 residual 的连接。最终的输出图像是三个子网络的输出的带权重的求和，权重是网络自己学的。训练方法是：最开始训练的时候，三个子网络单独各自训练，并且训练的开始阶段是不包含 non-local 的操作，之后以初始化 0 的方式加入到训练中。预训练完三个子网络之后，训练整个网络，用的损失函数是综合的损失函数。比较让人在意的地方是，作者指出，当采样率较低时，卷积核比较小的网络的重建效果更好，这一点值得去思考。

这一篇之所以精读，两个原因，一个是 non-local 的运用，需要思考这种结构的深层意义。第二个是作者实验的发现。

网络结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200216135928.png"/>

non-local 的结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200216135954.png"/>

下面重点说明 non-local 的计算。最开始有两层卷积首先计算 $\theta, \phi, g$，前两个是用来计算相互关系的系数 $f(i,j)$，$g$ 是用来作为该像素的特征表示。$\theta, \phi$ 的计算论文没有说明，似乎就是1x1卷积，但是不知道为什么示意图中是 1x1x1。

<img src="http://latex.codecogs.com/svg.latex? f\left(\mathbf{x}_{i}, \mathbf{x}_{j}\right)=\operatorname{ReLU}\left(\mathbf{w}_{f}^{T}\left[\theta\left(\mathbf{x}_{i}\right), \phi\left(\mathbf{x}_{j}\right)\right]\right)" border="0"/>

计算出相互关系后，和 $g$ 相乘进行合并，并且进行标准化：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{o}_{i}=\frac{1}{\mathcal{C}(x)} \sum_{\forall j} f\left(x_{i}, x_{j}\right) g\left(x_{j}\right)" border="0"/>

最后再经过一个运算（包含 resiudal 的部分），得到最终输出（之后还加了一层卷积）：

<img src="http://latex.codecogs.com/svg.latex? \tilde{z}_{i}=\mathcal{F}^{n l}(\mathbf{z})=W_{x} o_{i}+z_{i}" border="0"/>

### Bibtex:

```latex
@article{li2019multi,
  title={Multi-Scale Residual Reconstruction Neural Network With Non-Local Constraint},
  author={Li, Wan and Liu, Fang and Jiao, Licheng and Hu, Fei},
  journal={IEEE Access},
  volume={7},
  pages={70910--70918},
  year={2019},
  publisher={IEEE}
}
```

