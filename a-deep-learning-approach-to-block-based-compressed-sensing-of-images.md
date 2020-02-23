### Paper: 

A Deep Learning Approach to Block-based Compressed Sensing of Images

### Author:

Amir Adler , David Boublil , Michael Elad , Michael Zibulevsky

### Year:

2016

### Notes:

这篇文章和 [A Deep Learning Approach to Structured Signal Recovery](a-deep-learning-approach-to-structured-signal-recovery.md)基本一样。都是用一个几层的全连接网络来训练线性变换和重建。考虑的也是patch，所以这篇叫 block-based 压缩感知。区别在于激活函数，这篇文章用了ReLU，其他似乎没有什么本质的区别。简单说，就是用深度学习来训练一个非线性的重建过程。或者说，建立图像到测量值（隐变量）再到重建图像的映射。

比较的实验比较多。和许多基于block的传统压缩感知算法进行了比较。对于网络的层数、结点数、patch大小等也做了实验找最好的结果。另外这篇文章没有特别说怎么训练，损失函数之类的，只说用了Adagrad算法优化。

### Bibtex:

```latex
@article{adler2016deep,
  title={A deep learning approach to block-based compressed sensing of images},
  author={Adler, Amir and Boublil, David and Elad, Michael and Zibulevsky, Michael},
  journal={arXiv preprint arXiv:1606.01519},
  year={2016}
}
```