# not-so-BigGAN: Generating High-Fidelity Images on a Small Compute Budget

[TOC]

## 信息

Authors: Seungwook Han, Akash Srivastava, Cole Hurwitz, Prasanna Sattigeri, David D. Cox

Year: arXiv 2020

Bibtex:

```
@article{han2020not,
  title={not-so-BigGAN: Generating High-Fidelity Images on a Small Compute Budget},
  author={Han, Seungwook and Srivastava, Akash and Hurwitz, Cole and Sattigeri, Prasanna and Cox, David D},
  journal={arXiv preprint arXiv:2009.04433},
  year={2020}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/not-so-biggan-generating-high-fidelity-images-on-a-small-compute-budget.pdf)



## 概括

这篇文章的方法在我看来不是好的研究思路。BigGAN 在生成大尺寸的图像时，因为使用了大网络和大的 batchsize，所以对训练设备的要求非常高。这篇文章为了解决这个问题，提出的思路是用 BigGAN 生成一个低分辨率的图像，然后用生成的结果生成细节高频信息，用小波变换合成。换言之第二步中生成的是小波变换的高频部分。而第二步使用的网络则是 U-Net。低分辨率和目标分辨率之间有好几级的差距，所以需要多个 U-Net 作为decoder。在我看来目的就是超分辨，只不过不是用传统的超分辨网络而已。传统的超分辨网络在大尺度下仍然对设备有很多的要求。为了在小设备上训练，作者甚至对图像的高频部分做多次小波分解，U-Net 生成的是更细的 patch。



## 方法

![屏幕快照 2021-03-10 下午8.48.37](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-10 下午8.48.37.png)

这个图基本说明了这篇文章的方法。首先，对训练的图像做小波分解，得到 TL 图像，这些 TL 图像是用来训练 GAN 的。其他部分则是构成新的数据集来训练 decoder，decoder 就是从 TL 图像出发生成高频部分（三块，U-Net 的输出有三个部分）。图中绿色部分是高频部分更细的划分。至于这里更细的划分导致训练的时候 decoder 需要多少个，我觉得论文没说清楚。而且对于细分的高频 patch，它们有四块，从哪个部分作为网络输入生成呢？而且这个细分用的是小波变换来分解，非常怪异。

这个方法并没有真正解决问题，我觉得并不好。而且启发性完全不够。总而言之，这篇文章算不上好文章。