# Noise2Void - Learning Denoising from Single Noisy Images

[TOC]

## 信息

作者：Alexander Krull, Tim-Oliver Buchholz, Florian Jug

年份：2019

期刊会议：CVPR

Bibtex：

```latex
@inproceedings{krull2019noise2void,
  title={Noise2void-learning denoising from single noisy images},
  author={Krull, Alexander and Buchholz, Tim-Oliver and Jug, Florian},
  booktitle={Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition},
  pages={2129--2137},
  year={2019}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/noise2void-learning-denoising-from-single-noisy-images.pdf)



## 概括

通读。

这篇文章研究的是自监督去噪。方法从本质上来说和 Noise2Self 是一样的，但是可能训练的细节有一点区别。Noise2Self 是更广泛地描述，而 Noise2Void 应该看成一种特例。它的 J-invariant 性质则是每个像素看作是一个集合。因此可以理解成用周围其他像素来预测该像素的去噪结果。但是训练上，采用了更为有效率的做法，但同时我觉得稍微有一点偏离 J-invariant. 作者的做法是：将一个比 CNN 网络的感受野稍微大一点的 patch 作为网络输入，比如 64x64 patch，随机取 patch 上的 N 个点，N 取一个很小的数，并且让这些点是互不相邻的（可以说隔比较远）。此时用其他像素点来计算出一个值作为这 N 个点的替代。预测的时候，仅仅预测这 N 个点，也就是说 target 是噪声图片在这 N 个点的像素值。由于输入已经抹去了这 N 个点的信息，所以不可能训练成 identity 的函数。训练上和一般的监督学习没有区别。



## 方法

这篇文章的理论分析和 Noise2Self 是一样的，所以这里就不赘述了（不过作者没有分析）。训练的方法在概括中也进行了说明。通常 N = 64. 我一直很担心作者的实验是针对一张图片进行的，但实际上，作者的实验是对一个 noisy 数据集进行的。不过我认为确实也可以尝试对一张图来训练。因为作者都适用 patch 的形式训练的。

![屏幕快照 2021-12-14 下午3.51.38](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-12-14 下午3.51.38.png)

这个图很好地说明了作者的训练方式。b 图就是用蓝色方框的像素填补红色方框的像素。target 就只有红色方框的像素。其他的像素值是不计算 loss 的。为了更有效地训练，采取了大一点的 patch，只是同时选择了 N 个像素来进行监督学习。

从实验结果上说，是比不上 BM3D 的。我甚至在怀疑，BM3D 能不能用定理一来分析。毕竟 BM3D 也有一个优化过程。

很多实验细节在附录中，但是**没有给代码**，所以不太确定这个文章的实验是否有用。附录里给出了怎么填补抹掉的像素值的好几种方法，比如从给定范围的 neibor 像素中随机选择一个。

另外，作者尝试了 MSE 和 MAE 两种损失函数。

