### Paper:

Deep Neural Network Based Sparse Measurement Matrix for Image Compressed Sensing

### Author:

Wenxue Cui, Feng Jiang, Xinwei Gao, Wen Tao, Debin Zhao

### Year:

2018

### Notes:

阅读日期：2020.01.17

泛读。这篇文章研究的是CS重建问题，主要的着眼点在于采样部分。作者使用的采样方法不是高斯采样，而是使用了一个网络，通过学习来获得采样矩阵，其中创新的地方在于作者对采样的卷积参数有一些限制，一是稀疏性，二是标准化。具体做法就是对于卷积参数经过两次运算得到最终的的卷积参数，由此得到采样的值。在训练时，梯度计算会传到最原始的卷积参数，前传时都会使用稀疏性的预算（阈值）和标准化运算。对于测量值，作者用一个神经网络来重建（但这部分就非常简单了，不多赘述）。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-17%E4%B8%8B%E5%8D%888.19.17.png"/>

稀疏性运算：

<img src="http://latex.codecogs.com/svg.latex? \mathcal{S}\left(a_{k, i}\right)=\left\{\begin{array}{ll}{0} & {\left|a_{k, i}\right| \leqslant \mu} \\{a_{k, i}} & {\left|a_{k, i}\right|>\mu}\end{array}\right." border="0"/>

标准化运算：

<img src="http://latex.codecogs.com/svg.latex? \mathcal{F}\left(s_{k, j}\right)=\frac{s_{k, j}}{\sqrt{\sum_{i=1}^{N_{B}} s_{k, i}^{2}}}, \quad j=1,2, \cdots, N_{B}" border="0"/>

这两个运算都可以求导，因此可以对最原始的卷积参数进行更新。作者使用 2 范数的损失函数对采样网络和重建网络同时训练。算法如下：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-17%E4%B8%8B%E5%8D%888.54.54.png" width="60%"/>

### Bibtex:

```latex
@inproceedings{cui2018deep,
  title={Deep neural network based sparse measurement matrix for image compressed sensing},
  author={Cui, Wenxue and Jiang, Feng and Gao, Xinwei and Tao, Wen and Zhao, Debin},
  booktitle={2018 25th IEEE International Conference on Image Processing (ICIP)},
  pages={3883--3887},
  year={2018},
  organization={IEEE}
}
```

