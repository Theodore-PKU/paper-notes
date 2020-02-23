### Paper:

Learning Pyramid-Context Encoder Network for High-Quality Image Inpainting

### Author:

Yanhong Zeng, Jianlong Fu, Hongyang Chao, Baining Guo

### Year:

2019

### Notes:

阅读时间：2020.01.22

泛读。这篇文章研究的是 inpainting 问题。作者提出的做法是 pyramid-context 的方式进行重建。具体而言，就是在encoder 的不同 level 都进行 inpainting 的重建。这样做的好处是既有图像域的patch 信息，也有feature map 的语义信息。整体的框架是 U-Net，通过一个 ATN 结构来填补不同 level 的信息，ATN 结构可以理解为最接近patch加权填补和dilated convolution refine 两个步骤。损失函数使用了 pyramid 1-loss，decoder 部分会生成一个图像金字塔。另外还加上一个对抗学习。

网络结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-22%E4%B8%8B%E5%8D%889.00.45.png"/>

encoder 部分很好理解，不太清楚的地方是缺失部分的mask是如何保持的，是当作zero来处理？

ATN 结构可以看成有两个输入：

<img src="http://latex.codecogs.com/svg.latex? \psi^{l-1}=f\left(\phi^{l-1}, \psi^{l}\right)" border="0"/>

一个是当前的 feature map，有缺失，一个是根据上一层填补的结果作为 guidance。如果是 encoder 的最高 level，则没有对应的 ATN。上一层填补记为 $\psi^{l}$，当前层的 feature map 记为 $\phi^{l-1}$，则用前者 $\psi^{l}$ 计算缺失区域的 patch 和 outside 区域的 patch 的相似程度：

<img src="http://latex.codecogs.com/svg.latex? s_{i, j}^{l}=\left\langle\frac{p_{i}^{l}}{\left\|p_{i}^{l}\right\|_{2}}, \frac{p_{j}^{l}}{\left\|p_{j}^{l}\right\|_{2}}\right\rangle" border="0"/>

经过 softmax 计算得到权重：

<img src="http://latex.codecogs.com/svg.latex? \alpha_{j, i}^{l}=\frac{\exp \left(s_{i, j}^{l}\right)}{\sum_{i=1}^{N} \exp \left(s_{i, j}^{l}\right)}" border="0"/>

用这个权重填补 $\phi^{l-1}$ 的缺失区域：

<img src="http://latex.codecogs.com/svg.latex? p_{j}^{l-1}=\sum_{i=1}^{N} \alpha_{j, i}^{l} p_{i}^{l-1}" border="0"/>

注意 patch 从 $l$ 变成 $l-1$。对初步重建的 feature map，再经过一个4中 rate 的 dilated convolution，得到最终的 feature map。

decoder 部分则是将前一 level 的反卷积输出和 ATN 的输出合并后作为该层的输入，进行反卷积运算（$g$ 表示 反卷积，第 $l$ 层的输出是 $\varphi^{l}$）。

<img src="http://latex.codecogs.com/svg.latex? \varphi^{l-1}=g\left(\psi^{l-1} \oplus \varphi^{l}\right)" border="0"/>

损失函数：Pyramid L1 losses定义为：

<img src="http://latex.codecogs.com/svg.latex? L_{p d}=\sum_{l=1}^{L-1}\left\|x^{l}-h\left(\varphi^{l}\right)\right\|_{1}" border="0"/>

对抗loss 用的是如下形式：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} L_{D}=& \mathbb{E}_{x \sim p_{\text {data}}}[\max (0,1-D(x))] \\ &+\mathbb{E}_{z \sim p_{z}}[\max (0,1+D(z))] \end{aligned}" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? L_{G}=-\mathbb{E}_{z \sim p_{z}}[D(z)]" border="0"/>

个人感觉图像金字塔的结构一定是有用的。不过这里有一点疑惑是 encoder 的倒数第二层 ATN 的输入，直接用了上一层的 feature map，也就是没有填补，不知道这里怎么处理的。

### Bibtex:

```latex
@inproceedings{zeng2019learning,
  title={Learning pyramid-context encoder network for high-quality image inpainting},
  author={Zeng, Yanhong and Fu, Jianlong and Chao, Hongyang and Guo, Baining},
  booktitle={Proceedings of the IEEE conference on computer vision and pattern recognition},
  pages={1486--1494},
  year={2019}
}
```

