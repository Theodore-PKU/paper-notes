### Paper:

A Deep Learning Approach to Ultrasound Image Recovery

### Author:

Dimitris Perdios, Adrien Besson, Marcel Arditi, Jean-Philippe Thiran

### Year:

2017

### Notes:

这篇文章将深度学习的方法应用于超声的重建。使用的方法是比较早期的深度学习应用于压缩感知领域的方法，即多层全连接层（和多层感知机有什么区别...)。作者考虑了两种情形，一种是固定压缩感知方式（高斯随机矩阵），一种是由一个全连接层实现感知的学习。但是这和论文中关于超声领域信号获取方式确定，无法应用高斯或伯努利随机矩阵矛盾了。

作者回顾了压缩感知理论的内容，也讨论了传统方法在超声领域的限制。第一个就是测量矩阵的性质，超声的获取方式已经决定了不可能是高斯随机或伯努利随机；第二个是稀疏性的困难；第三个是时间慢。这篇文章最大的问题在于，没有说明清楚超声的具体模型，也没有说明网络的输入到底是什么。加之使用的方法过于老旧，没有参考意义。

### Bibtex:

```latex
@inproceedings{perdios2017deep,
  title={A deep learning approach to ultrasound image recovery},
  author={Perdios, Dimitris and Besson, Adrien and Arditi, Marcel and Thiran, Jean-Philippe},
  booktitle={2017 IEEE International Ultrasonics Symposium (IUS)},
  pages={1--4},
  year={2017},
  organization={Ieee}
}
```