# Cross-Scale Internal Graph Neural Network for Image Super-Resolution

[TOC]

### 信息

Authors: Shangchen Zhou, Jiawei Zhang, Wangmeng Zuo, and Chen Change Loy

Nanyang Technological University, SenseTime Research and Harbin Institute of Technology

```latex
@article{zhou2020cross,
  title={Cross-scale internal graph neural network for image super-resolution},
  author={Zhou, Shangchen and Zhang, Jiawei and Zuo, Wangmeng and Loy, Chen Change},
  journal={arXiv preprint arXiv:2006.16673},
  year={2020}
}
```

引用：3

[Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/cross-scale-internal-graph-neural-network-for-image-super-resolution.pdf)



### Code

https://github.com/sczhou/IGNN



### 概括

这篇文章题目叫 cross scale internal graph neural network，任务是超分辨，可以理解成是在超分辨任务中利用图像本身的 patch 信息，只不过这里的 patch 考虑了不同的尺度。这里的 graph 只是说考虑相似的 patch 的时候构成了一个图，但实际上和图卷积是没有关系的。虽然用 cross scale 但实际上并没有很多个 scale，这篇文章的做法其实是将同一张图像中和某个 patch 放大后的相似的 patch 信息利用起来。我个人觉得这个方法并没有什么特别通用性的启发。虽然是 NIPS，效果也确实很不错，但不具有很强的革新性。

简单地说明一下如何利用不同的尺度。首先假设 LR 图像和 HR 图像之间的放大倍数为 r，那么对 LR 图像做下采样，缩小倍数也为 r，缩小后的图像记为 LLR。设某个 LR 上的 patch 为 p，那么在 LLR 上的 p 位置的周围，寻找和 p 的具有相似特征的 k 近邻 patch，这些特征的提取是通过一个简单的卷积网络得到。找到 LLR 上的 k 近邻后，对应得到 LR 上的 k 个 patch，这些 patch 的 size 恰好是 p 的 r 倍。这些 patch 称为 HR patch，虽然它们并不是 HR 的图像的一部分。对 HR patch 进行进一步的特征提取得到一个新的 feature，这些 feature patch 进行标准化和加权计算（权值和 LLR 上的 k 个 patch 有关），得到一个融合了更大尺度的其他 patch 的信息。这个融合信息的 size 是 LR patch 的 r 倍。这个信息有两个用处，一个是经过一个下采样网络融合到 LR 图像部分，一个是 skip connection 到网络的后边。

这篇文章总体的思想是 non-local，但是对于超分辨任务，相同 scale 的其他 patch 的信息是没有特别用处的，反而是 scale 更大（就是 HR 相同的）patch 是有指导意义的。所以利用了不同 scale 的信息。也可以理解成在 LR 图像中寻找某些类似 HR 图像的信息。或者说，要求图像里相同纹理或相同事物的 patch 之间存在和放大倍率相同的大小关系。

反思：这里其实有一个小问题，就是对于 LR 图像中本身 scale 就比较大的 patch，这个方法应该就是失效的。



### 方法

作者的方法的出发点是图像的 cross-scale patch recurrence property. 这个性质很好理解，也很重要。作者把这个性质和 non-local 方法结合起来。non-local 方法的一般化形式是：
$$
\begin{equation}
\boldsymbol{Y}^{i}=\frac{1}{\delta_{i}(\boldsymbol{X})} \sum_{j \in \mathcal{S}_{i}} \boldsymbol{C}\left(\boldsymbol{X}^{i}, \boldsymbol{X}^{j}\right) \boldsymbol{Q}\left(\boldsymbol{X}^{j}\right), \forall i
\end{equation}
$$
简单来说，就是对 $X^j$ 的一个加权，权重和 $X^i, X^j$ 的相似性有关。这篇文章把比较的对象，从相同的 scale 换成了和超分辨有关的 scale。

这篇文章提出的方法称为 Graph Aggregation。

第一步：建立一个图。这里的图指的是要找到相似的其他 patch。假设超分辨的目标是放大 s 倍，那么从 LR 图像 $I_L$ 得到一个更小的下采样 s 倍的 $I_{L\downarrow s}$，这里的 $\downarrow$ 表示相对地进一步下采样。虽然后来的实验发现，下采样的倍数不一定要和 s 相同。因为可能下采样太多了找不到相似的 patch 了。通过一个 VGG19 网络的前三层，从 $I_L$ 和 $I_{L\downarrow s}$ 得到两个特征图 $E_L, E_{L\downarrow s}$。一个 $l \times l$ 的 query patch 在 $E_L$ 中，记为 $E_L^{q, l}$，然后在 $E_{L\downarrow s}$ 中的 $d \times d$ 的窗口中找到相似的 k 个 patch，记为 $E_{L \downarrow s}^{i, l}$，这些 $E^{i, l}_{L\downarrow s}$ 在 LR 图像中有对应的位置。由此得到了一个以 $E^{q,l}_{L}$ 为中心的图。这些图的节点就是 k 个 patch，边和 $E^{q,l}_{L} - E_{L \downarrow s}^{i, l}$ 有关，记为 $D^{i \rightarrow q}$。

第二步：信息融合。有了节点的位置，也有了节点之间的边的权重，现在要计算信息融合之后的结果。基本式子是：
$$
\begin{equation}
F_{L \uparrow s}^{q, l s}=\frac{1}{\delta_{q}\left(F_{L}\right)} \sum_{n_{r} \in \mathcal{S}_{q}} \exp \left(\mathcal{F}_{\theta}\left(\mathcal{D}^{n_{r} \rightarrow q}\right)\right) F_{L}^{n_{r}, l s}, \forall q
\end{equation}
$$
在这个式子里，$F$ 表示的是网络中间层的特征图。和 $E$ 是不一样的，$E$ 仅仅是为了找到 k 近邻的位置和权重的计算，但是 $F$ 是信息融合的要素。$F_{\theta}$ 是一个小型网络 edge-conditioned sub-network (ECN)，$\delta_{q}\left(F_{L}\right)$ 是归一化常数。不过由于 $L\downarrow s$ 的 k 个 patch 和 query patch 一样大，所以在 LR 图像中这些位置的 size 是 s 倍，类似的在 $F$ 中也是 s 倍。所以特征融合得到的是 $F_{L\uparrow s}$。这个融合的特征 patch 经过一个 downsampled-embedding sub-network (DEN) 进行下采样得到和 $F_L$ 相同的大小，并且和它 concatenation 的结果构成了下一层的网络输入。

第三步：adaptive patch normalization，在进行信息融合之前，作者还考虑对参与融合的 k 近邻特征图进行标准化，公式为：
$$
\begin{equation}
\operatorname{AdaPN}\left(F_{L}^{n_{r}, l s, c} \mid F_{L}^{q, l, c}\right)=\sigma\left(F_{L}^{q, l, c}\right)\left(\frac{F_{L}^{n_{r}, l s, c}-\mu\left(F_{L}^{n_{r}, l s, c}\right)}{\sigma\left(F_{L}^{n_{r}, l s, c}\right)}\right)+\mu\left(F_{L}^{q, l, c}\right)
\end{equation}
$$
这个公式也很好理解。

第四步： $F_{L\uparrow s}$ 除了 DEN 的输出，其本身也会通过 skip connection 和网络后面的特征图进行融合。

整个网络只有一个上述的模块，ECN，DEN 都是小的三层卷积网络。示意图如下：

![屏幕快照 2020-12-25 下午3.20.07](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-25 下午3.20.07.png)



### 实验及结果

由于使用了公开数据集，有很多比较对象。在这篇文章中，作者考虑了 2x 3x 4x 的放大。LR patchs 作为网络的输入固定是 60x60（换句话说对原始的图像进行了裁剪）。数据增广是旋转和翻转。

另外，作者还考虑了 self-emsemble 的结果（这个 emsemble 是集成的意思？）。

在 Graph Aggregation 中，query pathc 的 size 是 3x3. $d$ 的大小是 30/s。因为 Grapha Aggregation 这个模块是在原来的超分辨网络上增加的，所以作者先对原来的骨架网络进行了预训练。骨架网络的选择是 EDSR。说实话，EDSR 是一个非常好的模型了。（这篇 17 年的文章比后来的其他很多文章效果都好，这篇文章作为比较新的文章，引用的论文不知道是不是最好的模型了）。从指标上看，这篇文章的效果基本上优于其他方法。

![屏幕快照 2020-12-25 下午3.30.08](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-25 下午3.30.08.png)

作者还做了消融实验，验证网络中的一些组件的有效性。已经考虑了 Graph aggregation 模块的位置的影响。



