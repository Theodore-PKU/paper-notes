## When to use Convolutional Neural Networks for Inverse Problems

Author: Nathaniel Chodosh; Simon Lucey

Year: 2020

Published in: CVPR 2020

```latex
@inproceedings{chodosh2020use,
  title={When to use Convolutional Neural Networks for Inverse Problems},
  author={Chodosh, Nathaniel and Lucey, Simon},
  booktitle={Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition},
  pages={8226--8235},
  year={2020}
}
```

Link: https://github.com/Theodore-PKU/paper-notes/blob/master/when-to-use-convolutional-neural-networks-for-inverse-problems-2020-200702.md

Note: /Users/xieyutong/Documents/Research/PaperReading/Notes/when-to-use-convolutional-neural-networks-for-inverse-problems-2020-200702.md



这篇文章的关注点在于在逆问题中使用卷积网络是否是合适的。作者将卷积网络和卷积稀疏编码进行比较。

首先，作者基于其他人的研究成果，对 CNN 和 CSC 之间的关系进行了论述。通过 ISTA 算法中的软阈值函数去掉负数部分，就变成类似 ReLU 的函数。通过所谓的 hierarchy of sparsity 约束，就变成 CNN 了（估计就是在新的稀疏性约束下，迭代过程变成 CNN 网络了）。由此建立两者的关系。

在什么情况下，CNN 是有效的呢？作者认为当测量值 y 具有卷积压缩性的时候，CNN 是有效的，反之 CNN 是无效的。此时 CSC 的表现优于 CNN。测量值的性质和测量矩阵 M 有关。如果 M 具有卷积结构，那么 CNN 就是合理的。

在进行实验之前，作者给出了一个求解 CSC 问题的算法。不过这个部分就先忽略了。总的来说是一个通过优化算法构建的网络对卷积进行训练。

作者的第一个实验是将测量矩阵设计为 inpainting 的测量矩阵和一个对角块为随机线性变换的测量矩阵的加权。作者认为相邻像素之间存在关联时，测量矩阵的卷积结构保留，所以如果只是 missing data 或者 blurring，卷积结构都保留。当前者的权重较大时，卷积结构保留，当后者权重较大时，卷积结构被破坏。因此权重的变化和 CNN、CSC 的表现的差异可以说明作者的假设是否正确。

最后发现 CNN 效果变差的更为迅速。不过这里作者使用的 CNN 的基线网络感觉比较怪异，因为用的是一个感觉很不熟悉的网络。

第一个实验是一个模拟实验，之后作者对 JPEG 伪影去除任务作为实验对象。作者认为 JPEG 伪影去除问题中的测量矩阵具有 block diagonal 的结构。因为 JPEG 是基于 patch 的处理，所以和此前的对角块测量矩阵很相似。

作者还有其他实验，不过就不详细说明了。

总结起来，这篇文章作者的贡献是指出卷积网络适用性的问题，给出了一个 CSC 求解的新算法。





