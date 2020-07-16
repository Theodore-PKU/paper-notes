# Graph Structure of Neural Networks

Author: Jiaxuan You, Jure Leskovec, Kaiming He, Saining Xie

Year: 2020

Published in: ICML 2020



Link: https://github.com/Theodore-PKU/paper-notes/blob/master/graph-structure-of-neural-networks-2020-200715.md

Note: /Users/xieyutong/Documents/Research/PaperReading/Notes/graph-structure-of-neural-networks-2020-200715.md



这篇文章的目的是分析网络结构和模型表现之间的关系，旨在不通过训练，而是直接从网络结构的某些性质预测模型表现。具体的思路是：任何网络结构，可以构造一个“相关图”，分析相关图的两个度量，平均路径长度和聚类系数，与模型表现的关系。

先不讨论具体的定义。作者的做法是通过自己设计的一个图生成器，生成很多个相关图。这些生成的结果的两个度量的空间分布比较广泛。然后根据给定的网络架构，比如 MLP，Res-Net 等，从相关图变换出一个具体的网络，为了比较作者限制每个网络的参数计算量基本一致（这是通过调整网络的一些结构配置得到，基线的计算是计算基线网络的乘法和加法运算的个数）。对这些具体的网络进行训练，相同的网络架构之间进行比较，可以在度量空间中观察哪些类型的网络的效果好。

作者的主要发现是：

1. 相关图的度量空间，每个度量和模型表现构成一个较为平滑的二次多项式分布。最优表现出现在微笑曲线的最低处（靶点）。
2. 最优神经网络的度量和生物神经网络的度量比较接近。
3. 可以比较容易找到靶点的位置，因此可以较快地进行搜索和训练。

下面对细节进行说明，最主要对就是相关图的定义和构造。

![屏幕快照 2020-07-16 上午9.23.18](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-07-16 上午9.23.18.png)

这是相关图的表示。具体的含义是每一层网络由若干个节点表示，不同层的网络用相同的节点表示。每个节点之间是否有边相连取决于当前层的节点和下一层节点之间的运算关系。每个节点到自身有一个边，是因为这个节点和下一层对应节点之间有运算关系。这里的运算关系不是唯一的形式，根据不同的网络架构，为了都能有相关图的表示，采用不同的形式，节点表示的含义也是不同的。

比如，对于固定宽度的 MLP，每个节点可以表示一个标量（一般神经网络中的一个节点），对于可变宽度的 MLP，每个节点表示当层中的向量的部分分量。对于卷积网络，由于每一层通道数不同，所以相关图中的每个节点表示不同的通道数（2维size都是相同的）。文中的第二节有更具体的分析。

因为一个相关图只表示了一层，边对应的是上一层和下一层之间的关系，类似于信息传递。所以多层对应的是在这个图上的多轮信息传递。

边的具体含义表示为下式：
$$
\mathbf{x}_{v}^{(r+1)}=\operatorname{AGG}^{(r)}\left(\left\{f_{v}^{(r)}\left(\mathbf{x}_{u}^{(r)}\right), \forall u \in N(v)\right\}\right)
$$
AGG 一般是求和+激活函数，f 是线性变换。这里的 x 表示节点，上标 r 表示当前是哪一层。下表表示具体的节点。

类似的，也可以将相关图变换成网络结构（给定整体的网络架构）

![屏幕快照 2020-07-16 上午9.32.06](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-07-16 上午9.32.06.png)

![屏幕快照 2020-07-16 上午9.32.26](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-07-16 上午9.32.26.png)

这个表格给出了相关图的设定和不同网络架构之间的关系。

两个度量是平均路径长度和聚类系数，定义如下：

Speciﬁcally, average path length measures the average shortest path distance between any pair of nodes; clustering coefﬁcient measures the proportion of edges between the nodes within a given node’s neighborhood, divided by the number of edges that could possibly exist between them, averaged over all the nodes.

关于图生成器，说实话不是特别明白具体的做法，感觉给定网络架构，相关图都基本类似，不知道有什么差别。总之，作者用图生成器生成不同的相关图，并且使他们的两个度量分布比较广。

下面给出一些实验结果。

![屏幕快照 2020-07-16 上午9.35.41](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-07-16 上午9.35.41.png)



反思：这篇文章的目的其实和我们用线性区域数量类似，只不过我们使用的是线性区域数量这个度量，而这篇文章使用的相关图的两个度量。对于这篇文章，个人认为这个度量没有足够的数学理论支撑，只是一个实验现象。另外无法在不同架构的网络中进行比较，这是最大的问题，只能在相同架构的网络中比较不同的参数。另外就是如何确定靶点，说实话并不能很精准地知道哪个网络是最好的。

