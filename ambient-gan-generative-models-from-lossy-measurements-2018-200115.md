### Paper:

Ambient GAN: Generative Models from Lossy Measurements

### Author:

Ashish Bora, Eric Price, Alexandros G. Dimakis

### Year:

2018

### Notes:

阅读日期：2020.01.15

泛读。这篇文章要解决的问题是当完整的高质量的数据不足时，如何通过有测量损失的数据来训练生成模型。测量的方式多种多样，还可以包含随机性。作者在论文中提到了随机缺失、高斯卷积blurred+噪声、inpainting等。想法非常简单，对生成模型的输出做相应的变换，在变换域上进行判别。作者给出了一些理论的结论，但个人认为这些方法都很受限。最重要的假设是对于变换域下的分布，存在信号域上的分布与其对应，但这个并不是所有的问题背景都适用。

以高斯blurred + 噪声为例：倘若blurred 也不加，全是噪声图片，还能训练吗？如果生成图像也有噪声，就会变成噪声+噪声，可以区分，因此生成图像必须没有噪声，加了噪声之后才能混淆判别器？感觉这种做法并没有真正的好的理论依据。反而越发地和 DIP 相像起来，通过网络的正则项来限制信号的分布。

GAN 用的是 WGAN 的训练。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-15%E4%B8%8A%E5%8D%889.34.02.png"/>

### Bibtex:

```latex
@article{bora2018ambientgan,
  title={AmbientGAN: Generative models from lossy measurements.},
  author={Bora, Ashish and Price, Eric and Dimakis, Alexandros G},
  journal={ICLR},
  volume={2},
  pages={5},
  year={2018}
}
```

