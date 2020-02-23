### Paper:

Deep artifact learning for compressed sensing and parallel MRI

### Author:

Dongwook Lee; Jaejun Yoo; Jong Chul Ye

### Year:

2017

### Notes:

这篇文章解决的事欠采样MRI重建（CS-MRI）。主要想法是利用拓扑上的分析（伪影的流形维度更小，更容易学习），重建出 aliasing artifact，然后用zf重建图像减去 artifact 图像。作者认为伪影具有全局分布的特性，所以考虑了多尺度的神经网络（Unet 结构）。作者的实验主要是基于多线圈的MRI。 （这篇文章和作者 Jong Chul Ye 的另一篇做 CT 重建的想法基本一样 [Deep Residual Learning for Compressed Sensing CT Reconstruction via Persistent Homology Analysis](deep-residual-learning-for-compressed-sensing-ct-reconstruction-via-persistent-homology-analysis.md)）

作者认为应该通过简化数据的分布使得网络可以更好地学习（所以生成伪影），伪影的全局特性指导如何设计网络（更大的感受野）。从机器学习泛化误差的角度分析，使用伪影的好处是使得问题更简单，因此可以用更简单的网络来生成，泛化性更好。

作者分析了网络的各个层，采用的欠采样方式是笛卡尔采样，部分低频和高频的均匀随机采样。phase 网络会遇到一个问题：在噪声区域 phase 的值其实是杂乱无章的。作者的解决办法是先训练 magnitude 网络，在用这个网络来确定 ROI，在phase 的噪声区域就不考虑了。test 的重建过程也是先 magnitude，然后 phase

实验的比较的内容：图像的多尺度训练，伪影的单尺度训练，伪影的多尺度训练。另外作者提到因为采样方式，所以伪影有一致性。由于有一部分低频被采样了，所以基本上是边界上的伪影。

整体框架：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/pic1.png" style="zoom: 50%;" />

网络结构（Unet）：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-25%E4%B8%8A%E5%8D%8812.46.00.png" style="zoom: 50%;" />

### Bibtex:

```latex
@article{lee2017deep,
  title={Deep artifact learning for compressed sensing and parallel MRI},
  author={Lee, Dongwook and Yoo, Jaejun and Ye, Jong Chul},
  journal={arXiv preprint arXiv:1703.01120},
  year={2017}
}
```

