### Paper:

CSVideoNet: A Recurrent Convolutional Neural Network for Compressive Sensing Video Reconstruction

### Author:

Kai XU, Fengbo Ren

### Year:

2016

### Notes:

这篇文章讲的是用RCNN的结构实现对视频的压缩感知测量值的重建。CNN用作空间信息抽取，RNN用于时间域。最主要的想法是，把视频（文中处理短视频）的帧分为了key frame（前几帧）和non key frame。两者的区别在于前者提供了背景信息，后者只要提供动作信息。

网络结构：三个部分。1. 随机矩阵作为压缩感知测量。对key frame用较多的感知，对non key frame用较少的感知。2. CNN，相应地分为两个CNN，一个较大的网络用来提取key frame信息，一个较小的网络用来提取non key frame的信息。3. LSTM，每一帧用CNN提取的信息是LSTM的每个单元的输入，输出是图像的重建。

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-05%E4%B8%8A%E5%8D%8811.17.25.png)

使用了patch，猜测时间序列可能就是同一个位置的patch构成一个时间序列。

CNN的输入就是测量值 ，所以CNN的第一层是一个全连接层，转换为一个2d的图，之后再接卷积层。

训练：分为两个阶段。第一阶段，预训练key frame CNN，预训练的内容就是直接重建出frame，即大CNN的输出就是图像。第二阶段，key frame CNN 的最后两层参与训练，其他不动。整个网络进行端到端的训练。

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-05%E4%B8%8A%E5%8D%8811.17.37.png)

实验结果还可以。

### Bibtex:

```latex
@article{xu2016csvideonet,
  title={CSvideonet: A recurrent convolutional neural network for compressive sensing video reconstruction},
  author={Xu, Kai and Ren, Fengbo},
  journal={arXiv preprint arXiv:1612.05203},
  year={2016}
}
```