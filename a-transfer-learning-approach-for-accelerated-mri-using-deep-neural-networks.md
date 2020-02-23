### Paper:

A Transfer-Learning Approach for Accelerated MRI using Deep Neural Networks

### Author:

Salman Ul Hassan Dar, Muzaffer Özbey, Ahmet Burak Çatlı, Tolga Çukur

### Year:

2017

### Notes:

这篇文章的思路非常简单，即用其他数据集来训练重建网络，再用目标数据集来fine-tune，目的是解决数据集不足的问题。作者采用的网络结构是一个类似循环的结构，包含data fidelity 的重建项和神经网络重建模块。损失函数是网络重建结果和groudtruth 的1范数、2范数以及网络参数的正则项。

### Bibtex:

```latex
@article{dar2017transfer,
  title={A transfer-learning approach for accelerated MRI using deep neural networks},
  author={Dar, Salman Ul Hassan and {\"O}zbey, Muzaffer and {\c{C}}atl{\i}, Ahmet Burak and {\c{C}}ukur, Tolga},
  journal={arXiv preprint arXiv:1710.02615},
  year={2017}
}
```