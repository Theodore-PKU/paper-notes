### Paper:

Detecting GAN Generated Errors

### Author:

Xiru Zhu, Fengdi Che, Tianzi Yang, Tzu-Yang Yu, David Meger, Gregory Dudek

### Year:

2019

### Notes:

阅读时间：2020.01.30

泛读。这篇文章研究的是如何判断 GAN 生成的图像的好坏。作者认为现在常用的评价 GAN 生成图像质量的指标 IS 和 FID对于评价生成数据的整体表现是足够的，但是不适合评价单张图片。并且通过 FID 和 IS 我们不清楚什么地方是不同的。作者的想法是判断给定的像素是否来自真实的合理分布，因此使用了 self-attention layer 的技巧。网络结构是一个 U-Net 骨架，ResNet block，还包含了 VGG 的一些 pooling 层的结果（concatenation），self-attention layers 可以学到相距较远的像素的关系。作者没有给结构示意图。数据是真实图像和用某一种 GAN 训练后生成的图像，作者设计的损失函数有点类似于判别器（我觉得非常相似），通过极小化损失函数得到 error 的判定。当输出的某个像素的 PD 值较高，则说明这个像素值不真实。整幅图像的 PD 平均值可以用来评价这张图片的生成效果。作者做了一些实验，计算不同 GAN 的生成效果，还比较了 PD 值和 FID 值的关系。从论文中的图片可以看出，确实 PD 值高的图像（PD值可以用来排序，比较图像生成质量）更真实。

损失函数的定义为：

<img src="http://latex.codecogs.com/svg.latex? T\left(x_{q, i, j}\right)=\left\{\begin{array}{ll}{1} & {\text { if } x_{q, i, j} \text { is from real }} \\ {0} & {\text { if } x_{q, i, j} \text { is from generated }}\end{array}\right." border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \text { Loss }=E_{x_{q} \sim q}\left[\sum_{i, j}^{N, M} \lambda * T\left(x_{q, i, j}\right) *\left(P\left(E=1 | x_{q}, i, j\right)\right)+\gamma *\left(1-T\left(x_{q, i, j}\right)\right) *\left(P\left(E=0 | x_{q}, i, j\right)\right)\right]" border="0"/>

> 这个损失函数还是让真实图像判断像素错误的概率尽量是0，让生成图像判断像素错误的概率尽量是1，和判别器有什么区别？个人觉得这个网络就是一个判别器，只不过并没有继续训练生成器罢了。一般的判别器，如果在训练好之后继续用类似的损失函数，是否也能有一样的效果？还是说这篇文章的网络结构有特别之处？比如 self-attention layers，一般来说很少有文章对 GAN 的生成器有特别的设计。

### Bibtex:

```latex
@article{zhu2019detecting,
  title={Detecting GAN generated errors},
  author={Zhu, Xiru and Che, Fengdi and Yang, Tianzi and Yu, Tzuyang and Meger, David and Dudek, Gregory},
  journal={arXiv preprint arXiv:1912.00527},
  year={2019}
}
```

