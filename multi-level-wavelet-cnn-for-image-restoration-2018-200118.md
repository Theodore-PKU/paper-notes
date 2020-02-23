### Paper:

Multi-level Wavelet-CNN for Image Restoration

### Author:

Pengju Liu, Hongzhi Zhang, Kai Zhang, Liang Lin, and Wangmeng Zuo

### Year:

2018

### Notes:

阅读日期：2020.01.18

泛读。这篇文章研究的是去噪问题，作者的着眼点在于感受野和计算效率之间更好的平衡。作者提出的方法是将小波变换作为神经网络中的 pooling 和 unpooling 部分。作者对小波变换（包含下采样和上采样）的具体做法可以理解为小波变换——CNN——小波变换——CNN——小波变换——CNN——小波逆变换——CNN——小波逆变换——CNN——小波变换。这里的小波变换是对每个通道都展开为所有的 subband。CNN 的网络是4层没有 pooling 结构的网络。整体上是一个 U-Net，作者的想法可以看成是对 U-Net 结构的改进（事实上，这种 pooling 和 unpooling 结构本身和 U-Net 就是对应的）。

结构示意图

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-18%E4%B8%8A%E5%8D%888.33.24.png"/>

另外，作者使用的 U-Net 中，使用了 sum-connection，相当于residual learning。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-18%E4%B8%8A%E5%8D%888.53.28.png"/>

最后作者还分析了这种小波变换的 pooling 和 unpooling 与一般的 pooling、dilated convolution之间的对应关系。

从实验效果上看，有提升，但是不明显。感觉像炼丹的结果。个人感觉这里的CNN更像是局部特征的一种去噪。是不是还可以考虑在网络的中间输出层增加groudtruth 的噪声？

### Bibtex:

```latex
@inproceedings{liu2018multi,
  title={Multi-level wavelet-CNN for image restoration},
  author={Liu, Pengju and Zhang, Hongzhi and Zhang, Kai and Lin, Liang and Zuo, Wangmeng},
  booktitle={Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition Workshops},
  pages={773--782},
  year={2018}
}
```

