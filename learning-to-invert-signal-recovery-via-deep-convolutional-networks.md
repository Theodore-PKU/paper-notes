### Paper:

Learning to Invert: Signal Recovery via Deep Convolutional Networks

### Author:

Ali Mousavi and Richard G. Baraniuk

### Year:

2017

### Notes:

这篇文章是用深度卷积网络实现信号恢复，和这一篇（同作者）[A Deep Learning Approach to Structured Signal Recovery](a-deep-learning-approach-to-structured-signal-recovery.md) 基本思路相同，不过网络结构比以前复杂了。作者的基本想法就是先用测量矩阵的转置将测量值转为信号域，然后用一个卷积网络来生成原信号。从效果上来说，不及传统的算法（网络太简单了，只有三层），只是比较快。使用的loss 是mse。

网络结构：

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-25%E4%B8%8A%E5%8D%8810.59.40.png)

这篇文章的引用量还挺高的，可能是因为文章比较早吧。

### Bibtex:

```
@inproceedings{mousavi2017learning,
  title={Learning to invert: Signal recovery via deep convolutional networks},
  author={Mousavi, Ali and Baraniuk, Richard G},
  booktitle={2017 IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP)},
  pages={2272--2276},
  year={2017},
  organization={IEEE}
}
```