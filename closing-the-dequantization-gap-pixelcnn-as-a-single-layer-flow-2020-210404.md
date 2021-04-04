# Closing the Dequantization Gap: PixelCNN as a Single-Layer Flow

[TOC]

## 信息

Authors: Didrik Nielsen, Ole Winther

Year: NeurIPS 2020

Bibtex:

```latex
@article{nielsen2020closing,
  title={Closing the dequantization gap: Pixelcnn as a single-layer flow},
  author={Nielsen, Didrik and Winther, Ole},
  journal={arXiv preprint arXiv:2002.02547},
  year={2020}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/closing-the-dequantization-gap-pixelcnn-as-a-single-layer-flow.pdf)



## 概括

这篇文章我其实没有看懂。在 flow 模型中，由于是连续模型，但是图像是离散的，所以要用 dequantization 技术。在这篇文章中，作者构建了一个 subset flow，利用这个模型不需要使用 dequantization 也可以估计似然概率。然后结合了自回归模型什么的。但是完全没有看懂。最后还是放弃了。我认为现在对我来说 dequantization 并不是特别重要的问题，因为在医学图像中，基本上都是连续值的图像。

另外，在看论文的时候，想到一种连续化的思路。就是把原始数据，比如是 8 bit 的图像先变成 5 bit，然后用 5 bit 估计 8 bit，找出其中的变换关系，然后将这种关系迁移到 8 bit 到更高的 bit 上，这样就可以实现连续化了。原来的加 Uniform noise 的方法肯定是不够好的。另一个问题是图像本身就是连续的，只不过我们得到数值是离散的，所以这里本身就应该先变到一个足够好的连续值图像，而且似然函数就应当是估计连续值而不是离散值。



