### Paper:

A Deep Information Sharing Network for Multi-contrast Compressed Sensing MRI Reconstruction

### Author:

Liyan Sun, Zhiwen Fan, Yue Huang, Xinghao Ding, John Paisley

### Year:

2019

### Notes:

这篇文章关注的是 multi-contrast MRI 重建，也就是同一个部位的多种序列获得 的MRI图像的重建。这个问题的关键点在于不同序列成像对象是一样的，所以具有结构上的某种一致性。如何利用这部分信息是这篇文章的重点。其方法是在网络结构上进行改进。

在 Introduction 部分作者写到 The compressed sensing for magnetic resonance imaging (CSMRI) is also an active research topic in medical imaging, and now one of the classic inverse imaging problems in the ﬁeld of computer vision. 这是对 MRI 重建问题意义所在的一个很不错的写法。

作者回顾了传统的MRI 重建方法和深度学习方法，指出  DC-CNN （deep cascade）是深度学习的sota模型。对于 multi-contrast 问题，作者的出发点就是利用结构的相似性。作者在论文中给出了三个模型，依次改进，DIRN，DFSN，DISN。

对于multi-contrast 问题，作者回顾了传统的两种方法，Bayesian Compressed Sensing 和 FCSA-MT，并且指出了他们的一些缺点。BCS 方法利用的是 The prior distributions for each contrast share the same precision estimated in maximum likelihood fashion by the MRI with all the contrasts. FCSA-MT 方法利用的是非零小波系数基本都是相同位置，所以考虑 group sparsity。它的目标函数是
$$
\begin{aligned} x=\underset{x}{\arg \min } \frac{1}{2} & \sum_{i=1}^{L}\left\|F_{u_{i}} X(:, i)-y_{i}\right\|_{2}^{2} \\ &+\alpha\|X\|_{J T V}+\beta\|\Phi X\|_{2,1} \end{aligned}\\
\|X\|_{JTV} = \sum_{i=1}^{N} \sqrt{\sum_{s=1}^{L}\left(\left(\nabla_{1} X_{i s}\right)^{2}+\left(\nabla_{2} X_{i s}\right)^{2}\right)}\\
\|\Phi X\|_{2,1} = \sum_{i=1}^{N} \sqrt{\sum_{s=1}^{L}\left(\Phi X_{i s}\right)^{2}}
$$
这里tv和小波项都和single-contrast有一些区别。

下面介绍作者提出的三种模型。介绍之前，先说明一下基本的模块。主要是两个，一个是 Inference Block的卷积部分，一个是 Data fidelity Block。前者是一个多层卷积网络，但是最终输出是残差，所以有一个残差连接的结构。后者则是计算 $x_{i}=\underset{x_{i}}{\arg \min } \frac{\lambda_{i}}{2}\left\|F_{u_{i}} x_{i}-y_{i}\right\|_{2}^{2}+\frac{1}{2}\left\|x_{i}-x_{i n_{i}}\right\|_{2}^{2}$ . 因此这个模块就是用重建的结果来填补缺失的采样值，其余和原始的值做加权，即 $x_{i}=F^{H} \frac{\lambda F F_{u_{i}}^{H} y_{i}+F x_{i n_{i}}}{\lambda F F_{u_{i}}^{H} F_{u_{i}} F^{H}+I}$。这个模块的问题在于怎么求导，不过作者的说明不是很详细。理论上求导肯定是可以的，只是代码怎么写的问题。

**DIRN**

非常简单，每个序列用一个单独的DC-CNN 模型。

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-15%E4%B8%8B%E5%8D%8812.00.25.png)

**DFSN**

使用共享的特征图，也就是除了输入和输出是多个序列的结果以外，在 inference block 的卷积部分都是共享的。

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-15%E4%B8%8B%E5%8D%8812.00.33.png)

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-15%E4%B8%8B%E5%8D%8812.00.49.png" style="zoom:50%;" />

**DISN**

增加了dense 连接，也就是将每个inference block 的输出都作为后面block 的输入的一部分。原始的dense net 用的是所有的通道，为了减少参数量，作者只用了前面block生成的图像作为信息的传递。这是因为作者观察到靠前的block重建的图像也有表现优异的地方。

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-15%E4%B8%8B%E5%8D%8812.00.39.png)

训练：作者使用2范数的图像损失函数。数据集似乎都是公开数据集。数据量大小适中，大概几百个slice的pair。20%或10%的采样率。每个序列的采样都不同。使用笛卡尔采样。

比较对象是作者提出的三个网络，以及传统方法。另外作者探究了网络深度的影响，重建结果在分割任务上的效果。以及分析了配准的影响。因为要比较相同生理部位的不同成像，如果没有一致的位置，在传统方法里很难利用结构的一致性了。但是深度学习的方法比较明显地不需要考虑这个。因为它没有显式地利用这种相似性。

### Bibtex:

```
@article{sun2019deep,
  title={A deep information sharing network for multi-contrast compressed sensing MRI reconstruction},
  author={Sun, Liyan and Fan, Zhiwen and Fu, Xueyang and Huang, Yue and Ding, Xinghao and Paisley, John},
  journal={IEEE Transactions on Image Processing},
  volume={28},
  number={12},
  pages={6141--6153},
  year={2019},
  publisher={IEEE}
}
```

