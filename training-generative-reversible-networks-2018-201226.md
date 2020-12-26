# Training Generative Reversible Networks

[TOC]

### 信息

Authors: Robin Tibor Schirrmeister, Patryk Chrabaszcz, Frank Hutter and Tonio Ball

Translational Neurotechnology Lab, Medical Center, University of Freiburg, Germany

ICML 2018 Workshop

```latex
@article{schirrmeister2018training,
  title={Training generative reversible networks},
  author={Schirrmeister, Robin Tibor and Chrab{\k{a}}szcz, Patryk and Hutter, Frank and Ball, Tonio},
  journal={arXiv preprint arXiv:1806.01610},
  year={2018}
}
```

引用：4

[Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/training-generative-reversible-networks.pdf)



### Code

https://github.com/robintibor/generative-reversible



### 概括

yysy，这篇文章写得很烂。主要是没有突出文章做了一件什么事情。实验结果也很一般，所以这篇文章仅仅是作为一个简单的概述，没有其他内容。2018年的文章，引用数只有 4.

作者尝试将 RevNet 模型用于 adversarial autoencoder 模型。adversarial autoencoder 模型从文中的描述来看，我觉得是一个将对抗训练用于自编码模型的编码部分，而不是像 VAE 那样通过 KL 散度来控制编码的分布。RevNet 是一个类似于 Flow 模型的网络，具有可逆性。作者的想法就是 RevNet 作为 autoencoder 中的 encoder，同时也是 decoder。由于 RevNet 的可逆性，本身是不需要 reconstruction loss，但是作者考虑了一个 restricted latent space，也就是将 latent space 限制在更小的维度上（在原来的 RevNet 的编码的维度上强制取 0），在这种情形下导致不完全可逆，所以这部分的训练增加了 L1 的重建 loss。而这些 restricted 的维度的选择似乎是和未训练的 RevNet 的编码分布的方差决定的。

RevNet 的 block 结构基本和 Flow 模型是一致的。另外 squeeze 的模块也做了一些改动，使其不完全是 checkboard 类型。

剩下的就是论文中的示意图。

基本单元：

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-26 下午4.41.03.png" alt="屏幕快照 2020-12-26 下午4.41.03" style="zoom:50%;" />

squeeze：

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-26 下午4.42.36.png" alt="屏幕快照 2020-12-26 下午4.42.36" style="zoom:50%;" />

整体网络：

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-26 下午4.40.46.png" alt="屏幕快照 2020-12-26 下午4.40.46" style="zoom:50%;" />

参考文献：

Makhzani, A., Shlens, J., Jaitly, N., Goodfellow, I., and Frey, B. Adversarial Autoencoders. In International Conference on Learning Representations (ICLR), 2016. URL http://arxiv.org/abs/1511.05644. arXiv: 1511.05644. 这篇文章是作者提到的 adversarial autoencoder 的相关文献。