## DuDoRNet: Learning a Dual-Domain Recurrent Network for Fast MRI Reconstruction with Deep T1 Prior

Author: Bo Zhou; S. Kevin Zhou

Year: 2020

Published in: CVPR 2020

```latex
@inproceedings{zhou2020dudornet,
  title={DuDoRNet: Learning a Dual-Domain Recurrent Network for Fast MRI Reconstruction with Deep T1 Prior},
  author={Zhou, Bo and Zhou, S Kevin},
  booktitle={Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition},
  pages={4273--4282},
  year={2020}
}
```

Link: https://github.com/Theodore-PKU/paper-notes/blob/master/dudornet-learning-a-dual-domain-recurrent-network-for-fast-mri-reconstruction-with-deep-t1-Prior-2020-200702.md

Note: /Users/xieyutong/Documents/Research/PaperReading/Notes/dudornet-learning-a-dual-domain-recurrent-network-for-fast-mri-reconstruction-with-deep-t1-Prior-2020-200702.md



### 整体思路

这篇文章研究了 MRI 的重建（单线圈），整体的框架是 recurrent 类型的 image 重建 + kspace 重建 + DC 混合的网络。创新点有两个：1. 增加了 T1 数据的辅助（T1 是完全采样的），2. 每个子网络模块的结构上使用了 dilated 卷积 + residual + dense 的结构，相对比较复杂。

作者分析了之前的深度学习方法的三个缺陷。1. 没有考虑 kspace，对应就是作者的 recurrent 的部分包含 kspace 的子网络（实际上已经有很多文章使用了这种方法）；2. 网络结构的设计应该结合重建的特点，作者在这篇文章中使用了 dilated 卷积（说是从超分辨的文章启发而来）；3. 没有使用 T1 数据，对应作者把 T1 数据作为输入的一部分。

关于为什么可以用 T1 数据，作者的解释是 T1 数据的采样时间短，而其他核磁序列的采样时间长。但是这些图像的结构基本类似。所以 T1 完全采样，其他的序列欠采样。

总的目的是极小化图像、kspace 的 2 范数，同时保持 data consistency。实际的操作仍然是每个子网络计算一个 loss，data consistency 直接用传统的做法解决，所有的 loss 汇总直接优化。每个子网络共享参数（kspace 和 image 是分开的）

### 网络结构

**整体网络结构**：

![屏幕快照 2020-07-02 下午6.07.47](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-07-02 下午6.07.47.png)

由多个模块过程，每个模块包含三个部分，两个子网络，一个 DC 层。同时，每个子网络的输入都用 concatenation 的方式融合到输入中（分别由 kspace 和 image 两种 T1 数据）。

两种子网络的结构是相同的，如下图

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-07-02 下午8.35.57.png" alt="屏幕快照 2020-07-02 下午8.35.57" style="zoom:50%;" />

最开始有一个 3x3 卷积，其结果用 resiudal 的方式传到最后。第二个 3x3 卷积后面是 dilated 卷积 + dense 连接 + residual 的结构，这个部分用 1x1 卷积 + 3x3 卷积的到特征提取，见下图。

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-07-02 下午8.39.19.png" alt="屏幕快照 2020-07-02 下午8.39.19" style="zoom:50%;" />

不同层的 dilated rate 是不一样的，作者的想法是这样可以提取不同尺度的信息，最后在和输入做 residual 求和的时候，还有一步 Squeeze-and-excitation 的操作（但是不太了解这种方法）。

每一个子网络的输出对应一个 loss，最后全部加起来。

DC 层很简单：
$$
k_{i D R D_{n}}=\left\{\begin{array}{cl}
\frac{\lambda k_{i D R D_{n}}(z)+k_{u_{n}}(z)}{\lambda+1} & \text { if } M(z)=1 \\
k_{i D R D_{n}}(z) & \text { if } M(z)=0
\end{array}\right.
$$
最后的重建用 kspace + IFT。

### 实验

实验中，采样方式考虑了三种（笛卡尔、径向、螺旋）。比较的指标是 SSIM，MSE，PSNR。比较的方法并不多，而且都比较旧（出了唯一一个用了 T1 辅助的，都是18年以前的，这些旧方法都没有考虑 kspace 的）

**实验结果**

![屏幕快照 2020-07-02 下午8.41.18](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-07-02 下午8.41.18.png)

其他的实验包括比较不同的采样率的效果和不同网络组件的对比实验、网络 recurrent 的层数的比较。

值得说明的是比较网络组件。

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-07-02 下午8.46.13.png" alt="屏幕快照 2020-07-02 下午8.46.13" style="zoom:50%;" />

从这个表可以看出，贡献最大的其实是 kspace + image + recurrent 这个结构。作者提出的包含 dilated 卷积的复杂网络实际提升的效果并不大。虽然作者没有明确说 baseline 是什么样的（可能网络不使用 dilated 卷积吧），但是这个表格也说明作者之所以最后的效果那么突出，很有可能是因为比较对象没有使用 kspace + image + recurrent 的结构。另外从第一个实验的结果来看，T1 辅助的作用非常大，如果没有 T1，和其他的18年之前的方法比较没有那么大的提升。

### 总结

这篇文章启发性并不大，因为基本思想已经有工作做了，而提出的网络结构在对比实验中优势不太大，T1辅助这个想法也已经提出了，因此要有自己的新想法还得思考。