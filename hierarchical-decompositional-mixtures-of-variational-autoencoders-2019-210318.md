# Hierarchical Decompositional Mixtures of Variational Autoencoders

[TOC]

## 信息

Authors: Ping Liang Tan and Robert Peharz

Year: ICML 2019

Bibtex:

```
@inproceedings{tan2019hierarchical,
  title={Hierarchical decompositional mixtures of variational autoencoders},
  author={Tan, Ping Liang and Peharz, Robert},
  booktitle={International Conference on Machine Learning},
  pages={6115--6124},
  year={2019},
  organization={PMLR}
}
```

cite: 5



## 概括

这篇文章的思路是结合 SPN 和 VAE，SPN 的全称是 sum-product network。大致的做法就是将图像分成很多个小区域，这些小区域用 VAE 来生成，然后用 SPN 的结构组合起来。

文章里比较的对象是最简单的 VAE，这一点就让人不齿，实验的数据集也太简单了。

不喜欢这个思路，看了一半弃了。我觉得对于自己的课题没有什么参考意见。

不过这篇文章倒是提到了不少关于 VAE 的历史文献，这一点可以参考。