### Paper:

Deep Sparse Coding Using Optimized Linear Expansion of Thresholds

### Author:

Debabrata Mahapatra, Subhadip Mukherjee, and Chandra Sekhar Seelamantula

### Year:

2017

### Notes:

这篇文章和 [Learning optimal nonlinearities for iterative thresholding algorithms](learning-optimal-nonlinearities-for-iterative-thresholding-algorithms.md) 的想法基本一样，就是对 ISTA 算法展开成网络的形式，也是只训练激活函数非线性的部分。不过这篇文章使用的是 linear expansion of thresholds （LET），基函数是DoG的导数。作者在论文中表示这种表示更有效，需要优化的系数(大概为5个)，而对比文章中使用3次样条，参数很多。网络中只有激活函数的参数需要训练，其他部分和原来的ISTA算法一样，都是用测量矩阵A和测量值y（这两个都是给定的）来计算。除此之外，作者利用了FISTA算法中的加快迭代的做法，引入到网络中，构成残差的结构。

LET 的基函数：
$$
\phi_{k}(u)=u \exp \left(-\frac{(k-1) u^{2}}{2 \tau^{2}}\right), 1<k \leq K
$$
另外，作者给出了证明说任何一个LET函数，都可以找到对应的一个正则化函数，使得该激活函数是对应的proximal算子。

文章的大部分内容都在讲怎么优化算法，怎么计算系数。

fLETnet 的结构

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-16%E4%B8%8B%E5%8D%887.35.16.png)

### Bibtex:

```latex
@article{mahapatra2017deep,
  title={Deep sparse coding using optimized linear expansion of thresholds},
  author={Mahapatra, Debabrata and Mukherjee, Subhadip and Seelamantula, Chandra Sekhar},
  journal={arXiv preprint arXiv:1705.07290},
  year={2017}
}
```