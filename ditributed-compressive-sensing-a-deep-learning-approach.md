### Paper:

Distributed Compressive Sensing: A Deep Learning Approach

### Author:

Hamid Palangi, Rabab Ward, Li Deng

### Year:

2016

### Notes:

这篇文章的作者此前也有其他的和 multiple measurement vectors 的信号恢复的文章。比如 [Exploiting correlations among channels in distributed compressive sensing with convolutional deep stacking networks ](exploiting-correlations-among-channels-in-distributed-compressive-sensing-with convolutional-deep-stacking-networks.md) 以及 Reconstruction of Sparse Vectors in Compressive Sensing with Multiple Measurement Vectors using Bidirectional Long short-term Memory。这几篇都是一样的思想。这一篇和  Reconstruction of Sparse Vectors in Compressive Sensing with Multiple Measurement Vectors using Bidirectional Long short-term Memory 应该是差不多的，只不过写的更详细了（上一篇没怎么看，这一篇补上了）。在MMV问题中，原来的假设是信号的非零值位置是一致的。作者对该假设进行了放松，非零位置可能不一致，但是有相关性，因此训练一个神经网络的结构来发现这个相关性。

总结起来就是用 LSTM 来预测每一个要恢复的信号（稀疏域上）下一个加入支撑集的下标的概率。最开始支撑集是空集，用贪心的方法依次增加一个（即选择概率最大的）。在确定了新的支撑集之后，用最小二乘的方法计算恢复的信号。如何利用信号之间的相关性呢并且预测呢？作者的方法是用LSTM，输入是此前计算出来的残差，第一个信号只使用该信号的残差，之后的信号预测时使用之前的信号信息，所以就变成序列模型。

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-18%E4%B8%8B%E5%8D%882.30.33.png)

关于训练数据的获取方法，和 [Exploiting correlations among channels in distributed compressive sensing with convolutional deep stacking networks ](exploiting-correlations-among-channels-in-distributed-compressive-sensing-with convolutional-deep-stacking-networks.md) 几乎是一样的，target 是一个one-hot向量，表示下一个应该预测的分量的idx在哪里。

在此基础上，还可以考虑双向LSTM什么的。不过如何利用信号之间的相关性，确实是一个可行的方向。不过作者的这种预测+最小二乘的框架很难推广，计算量也很大。

### Bibtex:

```latex
@article{palangi2016distributed,
  title={Distributed compressive sensing: A deep learning approach},
  author={Palangi, Hamid and Ward, Rabab and Deng, Li},
  journal={IEEE Transactions on Signal Processing},
  volume={64},
  number={17},
  pages={4504--4518},
  year={2016},
  publisher={IEEE}
}
```