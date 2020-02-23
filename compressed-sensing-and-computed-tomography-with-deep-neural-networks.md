### Paper:

Compressed Sensing and Computed Tomography with Deep Neural Networks

### Author:

David Boublil, Michael Zibulevsky and Michael Elad

### Year:

2015

### Notes:

这篇文章（一个slice）使用神经网络来重建CT（low-dose）。但实际的做法是利用神经网络来融合信息。

思路：用传统算法得到不同的CT重建结果（比如迭代算法中的中间过程，不同的超参数）。将不同的结果作为神经网络的输入，得到最终的重建结果，网络结构在slice32页。需要大量训练。由于基本上是全连接层，所以使用了patch。

slice中有关于用测量值重建信号的部分。仍然需要思考的是如果用神经网络建立测量值到信号的映射，（这种情况往往只能用patch）那么信号的结构是如何被刻画的？

这篇文章的做法，其实是一种条件生成模型。如果把输入看成是不同的图像信息（low-dose，在某种程度就是有噪声，所以也是一个逆问题），那么这种模型也刻画了什么结构？难道是纯粹的拟合？如果是拟合，为什么这种层级、非线性、深度的架构可以表现得很好呢？

### Bibtex:

```latex
@article{boublilcompressed,
  title={Compressed Sensing and Computed Tomography with Deep Neural Networks},
  author={Boublil, David and Zibulevsky, Michael and Elad, Michael}
}
```