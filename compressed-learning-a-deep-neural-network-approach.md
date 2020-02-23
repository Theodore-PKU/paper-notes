### Paper:

Compressed Learning: A Deep Neural Network Approach

### Author:

Amir Adler, Michael Elad and Michael Zibulevsky

### Year:

2016

### Notes:

这篇文章是Compressed Learning。其实就是先用一个线性层做压缩感知，然后用卷积层实现某一个任务（比如分割）。线性层做压缩感知时，维数下降的还是很多的。具体做法上对线性测量值先做一个全连接，使维度恢复，然后在接卷积层（那为啥不直接重建再接分类网络呢？？）。这篇文章的作者和 A Deep Learning Approach to Block-based Compressed Sensing of Images的作者是一样的。不过这篇论文中，他们用了更复杂的（现在更常见的一些结构）网络。

### Bibtex:

```latex
@article{adler2016compressed,
  title={Compressed learning: A deep neural network approach},
  author={Adler, Amir and Elad, Michael and Zibulevsky, Michael},
  journal={arXiv preprint arXiv:1610.09615},
  year={2016}
}
```