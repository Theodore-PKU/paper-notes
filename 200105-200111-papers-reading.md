# 200105-200111 论文阅读总结

[TOC]

一共看了 19 篇论文。

## 论文列表

- [Efﬁcient B-mode Ultrasound Image Reconstruction from Sub-sampled RF Data using Deep Learning](https://github.com/Theodore-PKU/paper-notes/blob/master/efficient-b-mode-ultrasound-image-reconstruction-from-sub-sampled-rf-data-using-deep-learning-2018-200109.md)
- [Variable-Density Single-Shot Fast Spin-Echo MRI with Deep Learning Reconstruction by Using Variational Networks](https://github.com/Theodore-PKU/paper-notes/blob/master/variable-density-single-shot-fast-spin-echo-mri-with-deep-learning-reconstruction-by-using-variational-networks-2018-200109.md)
- Computational Optical Tomography Using 3D Deep Convolutional Neural Networks (3D-DCNNs)
- [Compressive Image Recovery Using Recurrent Generative Model](https://github.com/Theodore-PKU/paper-notes/blob/master/compressive-image-recovery-using-recurrent-generative-model.md)
- [A Segmentation-aware Deep Fusion Network for Compressed Sensing MRI](https://github.com/Theodore-PKU/paper-notes/blob/master/a-segmentation-aware-deep-fusion-network-for-compressed-sensing-mri-200110-2018.md)
- [Deep Tensor ADMM-Net for Snapshot Compressive Imaging](https://github.com/Theodore-PKU/paper-notes/blob/master/deep-tensor-admm-net-for-snapshot-compressive-imaging-200110-2019.md)
- [LEARN: Learned Experts’ Assessment-Based Reconstruction Network for Sparse-Data CT](https://github.com/Theodore-PKU/paper-notes/blob/master/learn-learned-experts-assessment-based-reconstruction-network-for-sparse-data-ct-2018-200110.md)
- [Content-Adaptive Image Compressed Sensing Using Deep Learning](https://github.com/Theodore-PKU/paper-notes/blob/master/content-adaptive-image-compressed-sensing-using-deep-learning-2018-200110.md)
- Image reconstruction by domain transform manifold learning
- [Are good local minima wide in sparse recovery?](https://github.com/Theodore-PKU/paper-notes/blob/master/are-good-local-minima-wide-in-sparse-recovery-2018-200110.md)
- [Highly Scalable Image Reconstruction using Deep Neural Networks with Bandpass Filtering](https://github.com/Theodore-PKU/paper-notes/blob/master/highly-scalable-image-reconstruction-using-deep-neural-networks-with-bandpass-filtering-2018-200110.md)
- Ghost Imaging Based on Deep Learning
- Deep Compressive Macroscopic Fluorescence Lifetime Imaging
- [LAPRAN: A Scalable Laplacian Pyramid Reconstructive Adversarial Network for Flexible Compressive Sensing Reconstruction](https://github.com/Theodore-PKU/paper-notes/blob/master/lappran-a-scalable-laplacian-pyramid-reconstructive-adversarial-network-for-flexible-compressive-sensing-reconstruction-2018-200111.md) （这一篇比较有意思）
- Deep Residual Network for Off-Resonance Artifact Correction with Application to Pediatric Body Magnetic Resonance Angiography with 3D Cones
- [Model-Based Learning for Accelerated, Limited-View 3-D Photoacoustic Tomography](https://github.com/Theodore-PKU/paper-notes/blob/master/model-based-learning-for-accelerated-limited-view-3-d-photoacoustic-tomography-2018-200111.md)
- Using Deep Neural Networks for Inverse Problems in Imaging
- [Robust Compressive Phase Retrieval via Deep Generative Priors](https://github.com/Theodore-PKU/paper-notes/blob/master/robust-compressive-phase-retrieval-via-deep-generative-priors-2018-200111.md)
- [Deep Learning Sparse Ternary Projections For Compressed Sensing of Images](https://github.com/Theodore-PKU/paper-notes/blob/master/deep-learning-sparse-ternary-projections-for-compressed-sensing-of-images-2017-200111.md)



分类统计

| 类别                         | 数量 | 类别                   | 数量 |
| ---------------------------- | ---- | ---------------------- | ---- |
| MRI                          | 4    | US                     | 1    |
| CT                           | 2    | Inverse Problem        | 2    |
| Snapshot Compressive Imaging | 1    | Adaptive sample scheme | 1    |
| IHT 算法                     | 1    | Ghost Imaging          | 1    |
| CS                           | 1    | other application      | 1    |
| review                       | 1    | CPR                    | 1    |
| ternary projection           | 1    |                        |      |



## Novelty

- MAP 的梯度迭代计算，图像的马尔可夫性质（LSTM？）
- 结合 high level information，考虑信息融合。
- 先重建低频部分，再重建高频部分。



## 个人思考

- 想到一个关于 DIP 的分析方式：逐层分析，分析优化特性，特别是网络参数随机初始化，只更新输入。相反的也许也可以。可能可以看出为什么这两种更新方法效果不一样，也许可以对卷积神经网络和刻画图像分布有一些关系。
- MAP 的梯度迭代写法和残差结构之间的关系。
- 图像的马尔可夫性质和一般的马尔可夫性质不同，但这种局部特性仍然很相似。
- 图像金字塔（多尺度）和图像概率的关系。
- 先低频部分后高频部分有减小搜索空间的效果。
- Bora 的方法需要思考零空间问题。
