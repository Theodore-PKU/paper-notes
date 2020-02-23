### Paper:

Multi-Scale Deep Compressive Sensing Network

### Author:

Thuong Nguyen Canh and Byeungwoo Jeon

### Year:

2018

### Notes:

阅读日期：2020.01.16

泛读。这篇文章研究的是图像压缩感知重建。没有什么特别的心意。特点是利用了小波变换（作者参考了一篇去噪的文章）。网络结构如下：

1. 小波变换
2. stride=B 的卷积层（没有 bias 和激活函数）作为 sampling，这个sampling 没有对小波的subband 单独执行。
3. 初步重建也是一个卷积层
4. enhance net-1: 一个简单的 CNN 网络 
5. enhance net-2：multi-level wavelet convolutional CNN

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-16%E4%B8%8B%E5%8D%884.55.07.png"/>

mulit-scale sampling 和 initial recosntruction 的结构如图所示，reshape & concat 的目的是输出变成一张和原图像分辨率相同的初始重建结果。

一些细节。训练方式分为三个阶段，对应 sampling-initial reconstruction、enhance net-1、enhance net-2。每个阶段的训练都是用图像域的 2 范数。并且在后一阶段的训练时，前一阶段的参数也会继续 fine-tuning。文章中没有说明 enhance net-2 的 MWCNN 结构，得去看原论文才行。

### Bibtex:

```latex
@inproceedings{canh2018multi,
  title={Multi-Scale Deep Compressive Sensing Network},
  author={Canh, Thuong Nguyen and Jeon, Byeungwoo},
  booktitle={2018 IEEE Visual Communications and Image Processing (VCIP)},
  pages={1--4},
  year={2018},
  organization={IEEE}
}
```

