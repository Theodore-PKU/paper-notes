### Paper: 

A Deep Learning Approach to Structured Signal Recovery

### Author:

Ali Mousavi, Ankit B. Patel, Richard G. Baraniuk

### Year:

2015

### Notes:

#### 主要想法

这篇文章首先讨论了压缩感知的框架，认为需要解决的问题有三个：

1. 如何重建
2. 如何设计度量算子
3. 如何刻画信号的结构。

分析了传统压缩感知方法如何回答上述问题：

1. 凸优化或贪婪算法
2. 线性随机度量矩阵
3. 信号在预先设计的变换域下是稀疏的

讨论了深度学习如何应用于压缩感知。作者使用的方法是用一个去噪自编码网络作为框架。对三个问题的回答是：

1. 训练过的网络，可以直接进行重建。不需要某种迭代算法
2. 如果用线性随机度量矩阵，那么网络就只包含测量值到信号的部分。如果用网络来表示（论文中用一层全连接层），那么网络就变成信号到信号。非线性或者自适应和非自适应有差别。
3. 没有说明深度学习框架下如何刻画信号的低维特性。

#### 网络结构和训练

如果是用线性随机度量矩阵，则是三层的全连接网络。设信号维度为N，测量值维度为M，通道数变化（从 输入开始）是M-N-M-N

如果是非线性变换度量，则是四层的全连接网络。通道数变化为N-M-N-M-N

使用sigmoid作为激活层，有偏置。非线性变换可以用其他的非线性函数，如果是恒同变换则退化为自适应训练。

损失函数是MSE。

训练方法是：预训练部分-逐层训练，估计每层都是用自编码来无监督地恢复输入（最后一层怎么训？）。整体训练。训练时在输入加了高斯噪声。由于全连接层，用整个图像，计算量太大，所以用了32x32的patch。

训练时patch不重叠，测试时patch重叠。

其他的一些细节：灰度图，scale图像到（0，1），高斯噪声为N(0, 0.2)

实验用一个图像数据集训练。考虑了线性和非线性两种度量方式的结果。还计算了恢复信号的稳定性。

#### 实验结果

和CS有关的算法比较。在复杂的纹理图像上表现好，在有大片相同或类似区域的图像上表现不是最好。稳定性是在测量值较少时很好，但测量值较多时没有D-AMP算法好。

#### 反思

要从CS的更广的框架下思考深度学习。深度学习实现测量方式（不论是线性还是非线性）都很直接，只要在训练集上自适应就可以。另外很多问题中，测量方式已经给定了。往往需要考虑的是如何刻画信号的结构。**重建方式是由刻画结构的方式来决定的**。传统CS的方法是用稀疏变换下信号是稀疏的来刻画，也由此来确定重建算法（凸优化）。**深度学习如何刻画信号，或者说如何降维**，信号本身是高维空间的低维流形，在深度学习中，网络结构本身也是某一种降维，但是这种把高维数据（但又是低维特点）映射到什么样的空间里。需要思考的是这样的问题。这个问题也就是prior的问题。**原来的稀疏变换的信号刻画的方式在函数空间上有什么几何性质**？

另外一个问题是如果把稀疏变换（比如小波）看成一种对图像空间的一种逼近（或离散），那么深度学习是什么样的逼近？**两者之间有什么关联**？图像这一类有结构的信号在连续空间上的刻画是什么样的？这是需要思考的另一个问题。

关于度量矩阵的思考：**自适应本身是一种控制？强化学习？每次测量时根据之前的结果修改**？这也是一个问题。需要看一下参考文献。

### Bibtex:

```latex
@inproceedings{mousavi2015deep,
  title={A deep learning approach to structured signal recovery},
  author={Mousavi, Ali and Patel, Ankit B and Baraniuk, Richard G},
  booktitle={2015 53rd Annual Allerton Conference on Communication, Control, and Computing (Allerton)},
  pages={1336--1343},
  year={2015},
  organization={IEEE}
}
```

需要参考的其他论文。

[12] M. L. Malloy and R. D. Nowak, “Near-optimal adaptive compressed sensing,” Information Theory, IEEE Transactions on, vol. 60, no. 7, pp. 4001–4012, 2014.

[13] J. Haupt, R. Nowak, and R. Castro, “Adaptive sensing for sparse signal recovery,” in Digital Signal Processing Workshop and 5th IEEE Signal Processing Education Workshop, 2009. DSP/SPE 2009. IEEE 13th, 2009, pp. 702–707.

[14] J. Haupt, R. Baraniuk, R. Castro, and R. Nowak, “Sequentially designed compressed sensing,” in Proc. IEEE Work. Stat. Signal Processing, 2012, pp. 401–404.

[15] J. D. Haupt, R. G. Baraniuk, R. M. Castro, and R. D. Nowak, “Compressive distilled sensing: Sparse recovery using adaptivity in compressive measurements,” in Proc. Asilomar Conf. Signals, Systems, and Computers, 2009, pp. 1551–1555.