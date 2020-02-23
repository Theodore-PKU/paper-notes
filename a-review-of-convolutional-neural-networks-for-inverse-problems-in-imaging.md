### Paper:

A Review of Convolutional Neural Networks for Inverse Problems in Imaging

### Author:

Michael T. McCann, Kyong Hwan Jin, Michael Unser,

### Year:

2017

### Notes:

这是一篇review文章，很多内容其实都很清楚，只记录一些有用的评论。

What is unique here is that the artifacts caused by direct inversion, especially in the sparse measurement case, are usually highly structured and therefore not good candidates for generic denoising approaches. Instead, the CNN is allowed to learn the speciﬁc character of these artifacts.

While not typically a problem when working with natural images or standardized datasets, there may be huge ﬂuctuations in the intensity or contrast of the measurements in certain inverse problems. To avoid a small set of images dominating the error during training, it is best to scale the dynamic range of the training set [23], [27]. Similarly, it may be advantageous to discard training patches without sufﬁcient contrast.

如何解决数据不足：One strategy to increase the training set size is data augmentation. Another strategy to effectively increase the training set size is to use a pretrained network. For example, [18] ﬁrst trains a CNN for image super-resolution with a large image dataset, then retrains with videos.

Understanding the learning minimization problem as a statistical inference can provide useful insight into the selection of the cost and regularization functions.

### Bibtex:

```latex
@article{mccann2017review,
  title={A review of convolutional neural networks for inverse problems in imaging},
  author={McCann, Michael T and Jin, Kyong Hwan and Unser, Michael},
  journal={arXiv preprint arXiv:1710.04011},
  year={2017}
}
```