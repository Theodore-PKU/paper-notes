### Paper:

ReconNet: Non-Iterative Reconstruction of Images from Compressively Sensed Measurements

### Author:

Kuldeep Kulkarni, Suhas Lohit, Pavan Turaga, Ronan Kerviche, and Amit Ashok

### Year:

2016

### Notes:

这篇文章考虑的是对自然图像的压缩感知重建。使用的是block CS。方法是用一个CNN网络来重建，输入是测量值。并且对重建的结果用BM3D的去噪方法去噪。CNN网络的第一层是全连接层。block 是不重叠的，大小为33x33，训练的patch数量大概为2w张。损失函数为图像域的2范数。对于不同的测量矩阵，如果要重新训练需要花很多时间，作者提出的策略是将卷积层部分固定，只fine-tuning 全连接层。作为早期应用CNN于压缩感知的文章，引用率很高。

### Bibtex:

```latex
@inproceedings{kulkarni2016reconnet,
  title={Reconnet: Non-iterative reconstruction of images from compressively sensed measurements},
  author={Kulkarni, Kuldeep and Lohit, Suhas and Turaga, Pavan and Kerviche, Ronan and Ashok, Amit},
  booktitle={Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition},
  pages={449--458},
  year={2016}
}
```