### Paper:

Deep Residual Learning for Compressed Sensing CT Reconstruction via Persistent Homology Analysis

### Author:

Yo Seob Han, Jaejun Yoo and Jong Chul Ye

### Year:

2016

### Notes:

在这篇文章中，作者提出了用一个U-net 网络来实现CT重建。比较新颖的地方在于，作者的训练目标不是干净的图像，而是伪影（也就是用带伪影的图像减去真实图像），输入仍然是用FBP算法得到的带有伪影的图像。因此网络实现的效果是提取伪影信息，最后用带伪影图像减去提取的伪影信息。

网络结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-18%E4%B8%8B%E5%8D%882.54.30.png" style="zoom:67%;" />

拓扑上的复杂性：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-18%E4%B8%8B%E5%8D%883.01.06.png" style="zoom:50%;" />

作者比较了直接学习生成图像和提取伪影的效果的差别、是否使用了不同尺度的信息，以及训练数据使用的 views 的数量不同时的差别。得出的结论是：1. 学习伪影效果更好（作者用一个拓扑的概念和拓扑的工具计算说明了伪影的复杂度低于实际图像）；2. 具有不同尺度的网络效果好于每一层都是相同尺度的网络；3. 使用较多的views训练得到的网络在更少views的数据上表现较差，所以应当使用各种views的数据来训练。

### Bibtex:

```latex
@article{han2016deep,
  title={Deep residual learning for compressed sensing CT reconstruction via persistent homology analysis},
  author={Han, Yo Seob and Yoo, Jaejun and Ye, Jong Chul},
  journal={arXiv preprint arXiv:1611.06391},
  year={2016}
}
```