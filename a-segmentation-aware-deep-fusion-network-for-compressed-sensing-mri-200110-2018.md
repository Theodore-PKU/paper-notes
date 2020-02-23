### Paper:

A Segmentation-aware Deep Fusion Network for Compressed Sensing MRI

### Author:

Zhiwen Fan, Liyan Sun, Xinghao Ding, Yue Huang, Congbo Cai, and John Paisley

### Year:

2018

### Notes:

阅读日期：2020.01.10

精读。这篇文章研究的是MRI重建。作者的主要想法是融合 high level information，帮助更好地重建。在这篇文章中，high level information 指的是分割任务中，分割网络的特征图。作者将分割网络中的信息和重建网络结合，但并不是以多任务的方式。作者在文中指出，虽然有其他的信息融合或多任务的方法，但目前很少有人将这种做法用于 MRI 重建。

首先，作者举了一个例子，完全采样和欠采样情况下，相较于整个图像的像素值分布的差异，分割的不同子区域的灰度值分布差别不是很大。这似乎说明分割信息是有用的。作者使用了三个网络，分别是分割网络、重建网络和信息融合网络。

<div align=center>
    <img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-10%E4%B8%8A%E5%8D%886.39.50.png" width="70%">
</div>

网络结构示意图 ：

<div align=center>
    <img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-10%E4%B8%8A%E5%8D%886.41.38.png">
</div>

分割网络使用了 U-Net，重建网络使用了 DC-CNN；信息融合网络基本框架也是一个重建网络，但是网络的每一层输入都经过了信息融合处理。具体的做法是，先预训练重建网络和分割网络（完全采样的数据，cross-entropy 的损失函数）；预训练后，将zero-fill重建图像输入重建网络，其结果输入分割网络，把U-Net 中的某些层的特征图提取出来，通过双线性差值得到一样的大小，concatenate 成一个 thick feature map，然后用一个 1x1 卷积压缩成 thin feature map，从 640通道变成32通道。这就得到了分割网络的信息。

融合信息的做法是把分割网络抽取的信息和信息融合网络中原来重建网络的特征图部分（并不是预训练的重建网络）合并，先经过一个1x1卷积变成和原来重建网络的特征图的通道数相同，再继续按照重建网络的方式接下一层卷积层。这样融合信息网络就不是原来的重建网络，而是融合了分割信息。这种融合可以看成是对重建网络中的每一个输入（也就是之前的输出），先用这种信息融合的方式处理之后再输入进网络中。

训练好重建网络和分割网络之后，只训练 deep fusion network，预训练的重建网络和分割网络不动。（但也可以考虑这种训练方式，有了第一个重建网络，经过分割网络得到信息抽取，经过融合网络得到新的重建，然后再经过分割网络得到新的信息抽取，然后再经过融合网络，依次类推，得到一个循环的结构。）

作者的实验结果表明，提出的融合分割信息的重建方法效果比和信息融合网络结构大小相似的重建网络更好。

### Bibtex:

```latex
@inproceedings{fan2018segmentation,
  title={A segmentation-aware deep fusion network for compressed sensing mri},
  author={Fan, Zhiwen and Sun, Liyan and Ding, Xinghao and Huang, Yue and Cai, Congbo and Paisley, John},
  booktitle={Proceedings of the European Conference on Computer Vision (ECCV)},
  pages={55--70},
  year={2018}
}
```