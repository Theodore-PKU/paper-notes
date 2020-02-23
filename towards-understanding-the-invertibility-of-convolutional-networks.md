### Paper:

Towards Understanding the Invertibility of Convolutional Neural Networks

### Author:

Anna C. Gilbert, Yi Zhang, Kibok Lee, Yuting Zhang, Honglak Lee

### Year:

2017

### Notes:

这篇文章是一个理论分析的论文，作者想分析的是卷积网络可以实现某种重建。但是没有看懂整个定理要说明的内容。首先作者假设的的稀疏信号，不是传统的稀疏，而是group内的稀疏，每个group的非零元素个数不多于一个。用这个定义的稀疏性向量线性组成一个空间来表示信号 $z$ 。

结合作者的回顾，对作者要试图证明的内容的理解和猜测大概是：如果我们假设信号z具有 group 稀疏性的结构，那么用一个随机生成卷积参数初始值的卷积网络 $f$（包含pooling），对输入 $x$, $x$ 是 z 经过某个测量变换得到的测量值，$f(x)$ 和 $x$ 之间的误差是可以被控制的。而这个网络本身具有对z这种信号的 RIP 性质。

因为没有完全看懂，所以实验部分跳过了。

唯一有一些用处的是，作者在回顾文献时提到了随机初始化的卷积网络具有的一些性质，比如直接用于分类也有一定的准确率（实际上还是有训练的参数，比如 softmax分类器的权重，BN层等，只是卷积核的参数是固定的，估计如果卷积的个数不够效果可能比较差）。这部分具有启发性，从某种意义上说，表明卷积网络的这种特征提取方式（如果理解成随机的超平面，就很直观）只要数量足够多，那么总是可以利用起来用于分类等任务。这也就能解释为什么训练好的网络具有可压缩性，因为大量的特征是冗余的。训练只是对网络的初始随机参数进行微调（超平面的微调）。这对于如何理解神经网络的机制有帮助。

### Bibtex:

```latex
@article{gilbert2017towards,
  title={Towards understanding the invertibility of convolutional neural networks},
  author={Gilbert, Anna C and Zhang, Yi and Lee, Kibok and Zhang, Yuting and Lee, Honglak},
  journal={arXiv preprint arXiv:1705.08664},
  year={2017}
}
```