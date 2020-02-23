### Paper:

Learned D-AMP: Principled Neural Network Based Compressive Image Recovery

### Author:

Christopher A. Metzler, Ali Mousavi, Richard G. Baraniuk

### Year:

2017

### Notes:

这篇文章的作者（其中两位）和 [Learning to Invert: Signal Recovery via Deep Convolutional Networks](learning-to-invert-signal-recovery-via-deep-convolutional-networks.md) 的作者是一样的（那篇的主要做法是用一个CNN来直接重建信号，也就是建立测量值到原信号的变换）。

这一篇作者的主要想法则是unfold一个迭代算法 D-AMP。作者首先将重建算法用一个谱来表示，一端是手动设计的算法（传统算法），一端是数据驱动的算法（直接用神经网络重建），中间则是unroll类型的算法，采用了传统的框架，使用数据来训练。作者对这些方法做了回顾，分析了优缺点。

作者其中提到了几何的角度思考重建问题。我认为值得从直观的角度想图像的几何空间上的分布。

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-25%E4%B8%8B%E5%8D%881.36.12.png)

接下来，作者描述了 D-IT 和 D-AMP 算法，前者是较简单的版本。

假设有一个去噪模型：$D_{\sigma}(x)=\operatorname{argmin}_{x}\left\|x_{o}+\sigma z-x\right\|_{2}^{2} \text { subject to } x \in C$

D-IT：
$$
\begin{aligned}
z^{t} &=y-\mathbf{A} x^{t} \\
x^{t+1} &=D_{\hat{\sigma}^{t}}\left(x^{t}+\mathbf{A}^{H} z^{t}\right)
\end{aligned}
$$
D-AMP:
$$
\begin{aligned}
b^{t} &=\frac{z^{t-1} \operatorname{div} D_{\hat{\sigma}^{t-1}}\left(x^{t-1}+\mathbf{A}^{H} z^{t-1}\right)}{m} \\
z^{t} &=y-\mathbf{A} x^{t}+b^{t} \\
\hat{\sigma}^{t} &=\frac{\left\|z^{t}\right\|_{2}}{\sqrt{m}} \\
x^{t+1} &=D_{\hat{\sigma}^{t}}\left(x^{t}+\mathbf{A}^{H} z^{t}\right)
\end{aligned}
$$
可以看出来，后者的改进的地方在与尽量让每次迭代时要去掉的噪声保持在加性高斯白噪声的分布，所以效果更好，分析见论文第四页。因为在 D-AMP 中需要通过采样来修正 ($b_t$)，所以作者设计的网络包含两个去噪模型，各自有不同的功能。不过D-AMP中有些细节不是很明白。

网络结构：

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-25%E4%B8%8B%E5%8D%881.36.22.png)

作者训练的内容只有两个去噪模型，测量矩阵 A 保持不变。在论文中，作者提出了三种训练方法：

- 端到端
- 逐层（目标仍是重建目标）
- 单独训练多个不同噪声水平下的去噪模型（单独的去噪问题优化）

作者比较提出的方法和其他方法，也比较了不同的训练方法。理论上也对训练方法做了分析，认为逐层和单独训练去噪模型是最好的（虽然实验中效果一般，但作者也给出了一定的解释）。欠采样的方式是2D高斯。

### Bibtex:

```latex
@inproceedings{metzler2017learned,
  title={Learned D-AMP: Principled neural network based compressive image recovery},
  author={Metzler, Chris and Mousavi, Ali and Baraniuk, Richard},
  booktitle={Advances in Neural Information Processing Systems},
  pages={1772--1783},
  year={2017}
}
```