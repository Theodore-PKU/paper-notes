### Paper:

Deep Fully-Connected Networks for Video Compressive Sensing

### Author:

Michael Iliadis, Leonidas Spinoulas, Aggelos K. Katsaggelos

### Year:

2016

### Notes:

这篇文章考虑的是视频的压缩感知重建问题。使用的方法是全连接的深度网络框架。

问题描述：视频的压缩感知有两种类型，时间或空间域的压缩感知，这篇文章考虑的是时间上的压缩。具体的压缩方式是：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-05%E4%B8%8B%E5%8D%884.35.48.png" style="zoom: 33%;" />

这种方式的压缩特点是，对每个位置的t维向量，压缩成了一个值。也就是说，压缩感知的方式没有考虑空间上的。另外，感知矩阵实际采用的是50%的伯努利矩阵。$\Phi=\left[\operatorname{diag}\left(\phi_{1}\right), \ldots, \operatorname{diag}\left(\phi_{t}\right)\right]$

实际的任务：用y重建x。作者考虑的是block(也就是在空间域上是patch)。理论上$\Phi$可以是完全不一样的，但是作者对每一个block用的是相同的感知矩阵。并且感知矩阵也是block形式。因此训练的时候就有很多个pair。作者先考虑了线性矩阵的重建方式 ：
$$
\min _{W_{p}}\left\|X-W_{p} Y\right\|_{2}^{2} \rightarrow W_{p}=\left(X Y^{T}\right)\left(Y Y^{T}\right)^{-1}
$$
对于单个y, x, 计算$W_p$是困难的，因为本身是一个超定方程。而且泛化性肯定很差。但是如果考虑很多pair，那么就可以找到一种平均意义上的结果（这也是损失函数的意义）。本来有很多个x可以对应同一个y，但是大部分的x都是不可信的，所以训练的好处就是用已知的x来限制。这种限制既有数据集的大小的限制，也有映射本身的限制。

训练：感知矩阵的block大小为$4\times4\times16$, 视频的block为$8\times8\times16$. 大概有$10^6$数量的pair. 作者先用了线性矩阵的形式作为映射，发现结果不错，然后就替换成深度网络。而网络的结构是全连接的。因为block的大小比较小，作者认为没有必要用卷积，所以考虑全连接。也因此为了避免参数量变大，作者使用的输入是测量值，而不是一个一般的重建$\Phi^Ty$。作者认为即使用重建以后的作为输入，效果差不多。

实现细节：对输入做了归一化操作。用SGD方法训练，网络的初始化也和现在常见的非常接近。

思考：不论是GAN，还是别的什么深度学习方法，也不论输入是测量值还是基本重建结果（即使是zf-recon 的mri也是信息比较少的，），都可以看成是如何将映射限制在真实的x的空间中。当然输入也会影响，因为这意味着输入空间的性质也许发生改变了。各种loss都是某一种限制，在这种重建问题上，需要考虑的是映射形式对于输出空间的影响。

### Bibtex:

```latex
@article{iliadis2018deep,
  title={Deep fully-connected networks for video compressive sensing},
  author={Iliadis, Michael and Spinoulas, Leonidas and Katsaggelos, Aggelos K},
  journal={Digital Signal Processing},
  volume={72},
  pages={9--18},
  year={2018},
  publisher={Elsevier}
}
```

