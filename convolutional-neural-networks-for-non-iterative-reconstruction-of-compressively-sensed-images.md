### Paper:

Convolutional Neural Networks for Non-iterative Reconstruction of Compressively Sensed Images

### Author:

Suhas Lohit, Kuldeep Kulkarni, Ronan Kerviche, Pavan Turaga and Amit Ashok

### Year:

2017

### Notes:

这篇文章的作者和 [ReconNet: Non-Iterative Reconstruction of Images from Compressively Sensed Measurements](reconnet-non-Iterative-reconstruction-of-images-from-compressively-sensed-measurements.md) 的作者一样，并且这篇文章相当于进阶版本。

框架是block-based，先用全连接层把测量值变成图像域，再接神经网络。首先是网络结构复杂化，其次是考虑了欧式距离作为loss以及再加上一个对抗loss的变种。网络结构是由 reconnet 模块，多个模块堆叠，reconnet 模块是11x11，1x1，7x7 卷积和 relu 的组合。

另外作者说，如果要训练测量矩阵，就再增加一个全连接层在网络的前端。作者认为全连接层的计算量太大，所以考虑用一个循环矩阵来替代（这样可以减少计算量）。

### Bibtex:

```latex
@article{lohit2018convolutional,
  title={Convolutional neural networks for noniterative reconstruction of compressively sensed images},
  author={Lohit, Suhas and Kulkarni, Kuldeep and Kerviche, Ronan and Turaga, Pavan and Ashok, Amit},
  journal={IEEE Transactions on Computational Imaging},
  volume={4},
  number={3},
  pages={326--340},
  year={2018},
  publisher={IEEE}
}
```