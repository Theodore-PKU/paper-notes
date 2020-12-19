# Comparison of maximum likelihood and gan-based training of Real NVPs

[TOC]

### 信息

Authors: Ivo Danihelka, Balaji Lakshminarayanan, Benigno Uria, Daan Wierstra, Peter Dayan

DeepMind, UCL

```
@article{danihelka2017comparison,
  title={Comparison of maximum likelihood and gan-based training of real nvps},
  author={Danihelka, Ivo and Lakshminarayanan, Balaji and Uria, Benigno and Wierstra, Daan and Dayan, Peter},
  journal={arXiv preprint arXiv:1705.05263},
  year={2017}
}
```

引用：54

[Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/comparison-of-maximum-likelihood-and-gan-based-training-of-real-nvps.pdf)



### 概括

这篇文章也非常简单，作者比较了 Real NVP 作为生成器的 GAN 模型的极大似然值和 Wasserstein 距离的结果。一种训练使用极大似然损失函数，一种训练使用 WGAN。

作者发现 WGAN 训练的网络并不会提升极大似然值。

作者还发现 Wasserstein 距离可以作为一种过拟合的度量（我不是很关心这个部分）

有一个比较有趣的发现时，对于不同的生成器模型（浅层和深层）极大似然训练的结果有很大差异。当浅层网络训练的时候，效果是非常差的。而 WGAN 训练的时候，即使是浅层网络，似乎还保持着一定的图像特点。

另一个有趣的发现是，利用生成网络的可逆性，对原始 image 的隐变量的一半做重新 sampling 的时候，极大似然训练的网络可以得到相对正常的结果，而 WGAN 则会得到一个破坏性的结果。我认为这个发现非常有价值。某种意义上说明了 GAN 模型和隐变量模型的一些本质区别。我认为这也说明了为什么 GAN 模型训练的似然函数值总是很差。因为并没有真正地把一个分布映射到另一个分布。这也反映出 GAN 生成的 image 和原始图像可能有较大的差异。但是 Flow-based 模型有可能会陷入 memorize 数据的问题。即它可能不会出现这种情况：z 的输出和颠倒的 z 的输出不是颠倒关系。因为数据里没有颠倒后的图像 x。这可能是一个很大的问题。

最后，作者还设计了一个辅助的判别器训练技巧（然而我觉得没有人用，所以就不管了）



### 方法和实验

基本的内容在概括中说明了。这篇文章并没有提出什么特别的东西。关于判别器训练的技巧，作者的想法是增加一些生成的 sample，把这些 sample 也放入判别器中（通过另一个网络的分支，进行特征提取），但是计算梯度更新生成器的时候，这部分 sample 是不参与的，参与计算的仍然是原来就输入到判别器中的 sample。

图示：

![屏幕快照 2020-12-19 下午3.26.32](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-19 下午3.26.32.png)

这里的 $x_e$ 就是额外 sample 的图像

最后附上一个图（partial sample，也就是对隐变量的重采样）：

![屏幕快照 2020-12-19 下午3.27.15](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-19 下午3.27.15.png)

