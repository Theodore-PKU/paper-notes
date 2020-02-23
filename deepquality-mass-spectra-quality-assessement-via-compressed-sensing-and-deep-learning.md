### Paper:

DeepQuality: Mass Spectra Quality Assessment via Compressed Sensing and Deep Learning

### Author:

Chunwei Ma

### Year:

2017

### Notes:

这篇文章要解决的是蛋白质质谱分析的质量评估问题。使用了压缩感知和深度学习的方法。实际上不是一个信号重建问题，而是一个感知学习问题(compressed learning)，也就是先将质谱分析用向量表达x，然后用感知矩阵得到y，作为网络的输入，用一个三层的卷积神经网络来实现分类。

### Bibtex:

```latex
@article{ma2017deepquality,
  title={DeepQuality: Mass Spectra Quality Assessment via Compressed Sensing and Deep Learning},
  author={Ma, Chunwei},
  journal={arXiv preprint arXiv:1710.11430},
  year={2017}
}
```