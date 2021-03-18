# Generating Diverse High-Fidelity Images with VQ-VAE-2

[TOC]

## 信息

Authors: Ali Razavi, Aäron van den Oord and Oriol Vinyals

Year: NeurIPS 2019

Bibtex:

```
@article{razavi2019generating,
  title={Generating diverse high-fidelity images with vq-vae-2},
  author={Razavi, Ali and Oord, Aaron van den and Vinyals, Oriol},
  journal={arXiv preprint arXiv:1906.00446},
  year={2019}
}
```

cite: 212

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/generating-diverse-high-fidelity-images-with-vq-vae-2.pdf)



## Code

https://github.com/deepmind/sonnet/blob/master/sonnet/python/modules/nets/vqvae.py

https://github.com/deepmind/sonnet/blob/master/sonnet/examples/vqvae_example.ipynb

https://tfhub.dev/deepmind/biggan-deep-256/1



## 概括

这篇文章结合了两种方法，一个是 VQ-VAE，一个是 PixelCNN，所以叫 VQ-VAE-2. VQ-VAE 并不完全按照 VAE 的框架来设计。这里有一个概念叫 codebook。encoder 获取的编码会和 codebook 中的编码进行比较，找到最接近的编码，然后用 codebook 中的编码去尽量重建图像（当然 codebook 是会被训练出来的）。为了生成高分辨率的图像，作者设计了 hierarchical 结构的 VQ-VAE。分辨率越大，层数越多。codebook 在训练好之后，用 PixelCNN 训练一个 prior 模型，用于生成 codebook，这些生成的 codebook 就可以用于重建图像。我这里的疑问是，既然用 PixelCNN 来生成 codebook，为什么非要用固定的 codebook 呢？直接生成 encoder 的编码不可以吗？



## 方法

方法的示意图：

![屏幕快照 2021-03-18 下午4.04.47](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-18 下午4.04.47.png)

可以概括为：ﬁrst, we train a hierarchical VQ-VAE to encode images onto a discrete latent space, and then we ﬁt a powerful PixelCNN prior over the discrete latent space induced by all the data.
$$
\begin{equation}
\mathcal{L}(\mathbf{x}, D(\mathbf{e}))=\|\mathbf{x}-D(\mathbf{e})\|_{2}^{2}+\|s g[E(\mathbf{x})]-\mathbf{e}\|_{2}^{2}+\beta\|s g[\mathbf{e}]-E(\mathbf{x})\|_{2}^{2}
\end{equation}
$$
这个是 VQ-VAE 的损失函数。作者在这篇文章中提出用一个移动更新的方式替换了第二项，但是我没看懂。

encoder 和 decoder 本身的结构比较简单，没有什么特别之处。至于 PixelCNN，作者使用了 self-attention layer。



## 反思

这篇文章的思路不是我喜欢的类型，hierarchical 的结构目前来看不算特别。但是对于 PixelCNN 我个人觉得并不合适，因为生成更大分辨率的图像时，PixelCNN 也会更庞大。另外 codebook 具体有多大，这篇文章始终没有提到，非常让人困惑。

不过这篇文章提到了一个很重要的信息，就是有的图像具有对称性，有的不具有，而这种特性并不能完全由随机性来决定，而是由图像的结构来决定。换句话说，像素之间不是完全独立的。即使考虑了 hierarchical 的结构，这种特性如果没有特别设计，也很难完全实现。