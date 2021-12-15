# Rethinking Deep Image Prior for Denoising

[TOC]

## 信息

作者：Yeonsik Jo, Se Young Chun, Jonghyun Choi

年份：2021

期刊会议：ICCV 2021

Bibtex：

```latex
@inproceedings{jo2021rethinking,
  title={Rethinking deep image prior for denoising},
  author={Jo, Yeonsik and Chun, Se Young and Choi, Jonghyun},
  booktitle={Proceedings of the IEEE/CVF International Conference on Computer Vision},
  pages={5087--5096},
  year={2021}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/rethinking-deep-image-prior-for-denoising.pdf)

code: https://github.com/gistvision/DIP-denosing



## 概括

通读。

这篇文章研究的是自监督去噪问题。基本的想法是基于 DIP，也就是说每一个图像是单独训练的。这篇文章的着眼点是如何确定停止训练。这篇文章借助了一个叫自由度 DF 的概念，这个概念提供了对于 fitting model 的一种量化指标。作者基于 DF 来检测 DIP 的方法是否过拟合，以此来确定什么时候停止训练。很神奇的是，这篇文章最后退出来的式子就是一个 SURE 的函数。换言之，作者用个SURE 函数来当作是 single image 上的训练。最后作者还有一些额外的技巧，不过我并不关注这一类的自监督方法，因此我们并不仔细看这个部分。



## 方法

这个小节我们着重分析为什么作者最后推出了 SURE 函数。

首先，作者把 DIP 的输入替换成了噪声图片，因此输入和输出都是一样的。

作者考虑的 DF 的定义是：
$$
\mathrm{DF}(\mathbf{h})=\frac{1}{\sigma^{2}} \sum_{i=1}^{n} \operatorname{Cov}\left(\mathbf{h}_{i}(\cdot), \mathbf{y}_{i}\right)
$$
这个地方之所以使用 $h(\cdot)$ 是因为不确定网络输入是什么。但是当选择 $y$ 作为网络输入的时候，就可以发现：
$$
\frac{1}{\sigma^{2}} \sum_{i=1}^{n} \operatorname{Cov}\left(\mathbf{h}_{i}(\mathbf{y}), \mathbf{y}_{i}\right)=\mathbb{E}\left[\sum_{i=1}^{n} \frac{\partial \mathbf{h}_{i}(\mathbf{y})}{\partial \mathbf{y}_{i}}\right]
$$
这个式子就是和 SURE 有关的。作者也承认了这一点。

作者如何推导出最后的损失函数呢？作者利用的是下面这个式子：
$$
2 \sigma^{2} \cdot \mathrm{DF}_{G T}(\mathbf{h}) \approx \mathcal{L}(\mathbf{x}, \mathbf{h}(\cdot))-\mathcal{L}(\mathbf{y}, \mathbf{h}(\cdot))+\sigma^{2}
$$
也就是说，作者希望 DF 的结果要越小越好。最后就推出了：
$$
\eta(\mathbf{h}(\mathbf{y}), \mathbf{y})=\mathcal{L}(\mathbf{y}, \mathbf{h}(\mathbf{y}))+\underbrace{\frac{2 \sigma^{2}}{N} \sum_{i=1}^{N} \frac{\partial \mathbf{h}_{i}(\mathbf{y})}{\partial(\mathbf{y})_{i}}}_{\text {divergence term }}-\sigma^{2} .
$$
这个和 SURE 一模一样的式子来。

最后作者提出的一个停止条件就是 loss 变成 0（大概是这样子吧）。

更多细节就不看了。从结果上来说，我觉得 BM3D 才是最好的。