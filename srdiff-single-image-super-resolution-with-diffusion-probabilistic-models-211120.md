# SRDiff: Single Image Super-Resolution with Diffusion Probabilistic Models

[TOC]

## 信息

作者：Haoying Li, Yifan Yang, Meng Chang, Huajun Feng, Zhihai Xu, Qi Li and Yueting Chen

期刊会议：arXiv

年份：2021

Bibtex：

```
@article{li2021srdiff,
  title={SRDiff: Single Image Super-Resolution with Diffusion Probabilistic Models},
  author={Li, Haoying and Yang, Yifan and Chang, Meng and Feng, Huajun and Xu, Zhihai and Li, Qi and Chen, Yueting},
  journal={arXiv preprint arXiv:2104.14951},
  year={2021}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/srdiff-single-image-super-resolution-with-diffusion-probabilistic-models.pdf)



## 概括

详读。

这篇文章研究的是超分辨问题，利用 ddpm 模型来解这个逆问题，提出了 SRDiff。基本的做法和 SR3 差别不大，仅仅是低分辨率图像作为输入，先经过一个预训练的 RRDB 模块，然后再融合到 ddpm 的网络中。另外，这篇文章的扩散过程定义在 residual 图像上，也就是低分辨率图像上采样和高分辨率图像的差值。换句话说，扩散模型生成的是 residual，需要和低分辨率图像结合得到最终的图像。这篇文章在实验上使用了不同的数据集，和 SRFlow、GAN 方法和一般的超分辨方法进行了比较（仅仅输出一个图像）。关于实验的比较，我认为是比较重要的部分，模型和方法本身没有什么特别的地方。另外作者使用的模型似乎比较简单，比较小，没有 attention layer 之类的较为复杂的模块（具体可以看方法中的图）。

这篇文章需要注意的是比较实验的结果。



## 方法

作者把超分辨任务分成了三种方法，第一种是 PSNR 导向型的方法，第二种是 GAN 生成的方法，第三种则是 flow-based 方法 SRFlow，前两种方法都无法实现 one-to-many 的生成。

ddpm 的方法在这篇文章的应用没有什么特别的。值得注意的是：1. 扩散过程定义在 residual 图像上，2. 低分辨率图像经过 RRDB 模块进行特征提取，3. 网络模型比较小，没有太多复杂的结构，所以训练起来也比较轻松。

![屏幕快照 2021-11-20 下午7.40.49](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-11-20 下午7.40.49.png)

![屏幕快照 2021-11-20 下午7.41.14](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-11-20 下午7.41.14.png)

RRDB 模块实际上就是去掉 RRDB 网络的最后一个卷积层。提取的特征使用了最简单的融合方式（应该就是合并 channel）。

网络的训练使用了 1 范数。

采样算法则和 ddpm 完全一致。

![屏幕快照 2021-11-20 下午7.44.19](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-11-20 下午7.44.19.png)



## 实验

这里我们并不对实验的具体细节，包括数据集、数据处理等。我们只是记录比较结果和作者的分析。

以其中一个数据集的实验为例：

![屏幕快照 2021-11-20 下午7.34.19](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-11-20 下午7.34.19.png)

作者比较了不同的方法（三种），比较的指标也很丰富。其中 LPIPS 是最近提出的一种相似性度量。LR-PSNR 则是比较生成结果和低分辨率图像直接的一致性。$\sigma$ 是标准差的比较。很显然，PSNR 最好的仍然是 RRDB，我认为这应该是目前最好的 PSNR 方法。SRDiff 在 PNSR 上无法超过 RRDB 和 EDSR，SSIM 也如此。但是作者并没有对这个进行过多的解读，仅仅表示生成的结果足够好。这也非常正常，因为生成多种可能的结果就意味着 PSNR 一定比较差。但是作者并没有将 SRDiff 在生成的多样性上和 SRFlow 进行比较，作者没有分析说到底哪一个后验分布估计得更准确。我认为这依然是一个大问题。GAN 方法则显得比较差。不过，RRDB 这个模型到底有什么特点，我觉得是非常值得关注的，之后要看看，这篇文章在超分辨领域到处都在引用，而且也作者 SRFlow 和 SRDiff 的低分辨率特征提取模块了。



