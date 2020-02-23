### Paper:

Deep Convolutional Framelet Denosing for Low-Dose CT via Wavelet Residual Network

### Author:

Eunhee Kang, Won Chang, Jaejun Yoo, and Jong Chul Ye

### Year:

2018

### Notes:

这篇文章是关于去噪的，所以实验部分读的比较粗略，主要关注了作者的方法和分析。这篇文章要解决的问题是 low-dose CT 重建，所以难点在于如何克服低剂量带来的噪声问题。

作者首先介绍了基于框架的去噪方法 frame-based denoising。给出了常见的一个迭代算法：
$$
f_{n+1}=\mu g+(1-\mu) \mathcal{W}^{\top} T_{\lambda}\left(\mathcal{W} f_{n}\right)
$$
之后作者试图分析深度学习和框架之间的关系，认为深度卷积网络也可以看成是一个framelet。其中**作者提到了使用较少数量的filters，相当于shrinkage的效果，或者是一个低秩分解的效果（最后作者也做了相关实验来验证低秩分解的效果）**。但是作者没有分析激活函数，总之这一块我认为作者的分析并不是很完美。基于这种想法，作者提出的去噪网络就类似于一个自编码器，一部分是  analytic operator，一部分是 synthsis operator. 因为没有shrinkage，所以相对应的迭代算法就是：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-24%E4%B8%8A%E5%8D%8810.43.41.png" style="zoom:50%;" />

关于这种迭代，如果只算一次 ，作者将其称为前传方法，如果迭代多次，就是一个RNN的结构。另外**作者考虑了两种输出，一种是输出噪声（也就是需要再最后一层加上输入或者用输入减去最后一层输出当作是去噪结果），另一种是直接输出去噪结果。作者认为由于前者的输出的流形维度更低，应该更好训练**。

网络结构和训练方面，作者将网络的输入和最终的输出都用 contourlet 系数表示，也就是先对图像做 contourlet 变换，一共得到15个通道（最终输出也是15个通道）。使用了 patch 的训练方式。为了利用较少的通道数实现低秩分解，作者计算出充分的通道数（充分是指可以完美重建，但不知道如何计算的，作者引用了一篇文章）为270（patch size 为 55x55），因此使用了128通道数作为低秩分解的手段

> 思考：如果把深度网络当作一个framelet，那么自编码形式确实非常合理，但是作者在这里并没有一个对framelet的重建增加一些限制。比如如果是一个低秩图像，那么它的重建应该本来就是完美重建？？

### Bibtex:

```latex
@article{kang2018deep,
  title={Deep convolutional framelet denosing for low-dose CT via wavelet residual network},
  author={Kang, Eunhee and Chang, Won and Yoo, Jaejun and Ye, Jong Chul},
  journal={IEEE transactions on medical imaging},
  volume={37},
  number={6},
  pages={1358--1369},
  year={2018},
  publisher={IEEE}
}
```