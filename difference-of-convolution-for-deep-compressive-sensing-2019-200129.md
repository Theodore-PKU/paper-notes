### Paper:

Difference of Convolution for Deep Compressive Sensing

### Author:

Thuong Nguyen Canh and Byeungwoo Jeon

### Year:

2019

### Notes:

阅读时间：2020.01.29

泛读。这篇文章研究的 BCS 的图像重建，基本方法和此前的 [Multi-scale deep compressive sensing network](multi-scale-deep-compressive-sensing-network-2018-200116.md) 这篇文章一样，区别在于那篇文章使用的是小波分解，这篇文章使用的是 DoG 分解。

DoG 分解就是用不同的高斯核进行模糊，相邻尺度之间求差。可以表示为（可以用卷积来计算）：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{x}_{i}=\left\{\begin{array}{ll}{\boldsymbol{x}-\boldsymbol{x} \otimes \boldsymbol{K}_{1}} & {\text { if } i=1} \\ {\boldsymbol{x} \otimes \boldsymbol{K}_{i}-\boldsymbol{x} \otimes \boldsymbol{K}_{i+1}} & {\text { if } i=2, \ldots, n-1} \\{\boldsymbol{x} \otimes \boldsymbol{K}_{n}} & {\text { if } i=n}\end{array}\right." border="0"/>

DoG 分解过程：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-29%E4%B8%8B%E5%8D%883.15..png" width="80%"/>

损失函数使用 2 范数 loss，整体的训练过程同样是分成三个阶段。另外也可以把 DoG 中的高斯卷积替换成可学习的卷积层。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-29%E4%B8%8B%E5%8D%883.15.4.png"/>

从最终的实验结果来看，效果并没有比小波分解的好多少。另外作者的这个方法仅仅局限在采样之前的预处理，对于重建本身没有太多借鉴。不过如何采样，不同的采样值是否具有相同地位这个值得思考。

### Bibtex:

```latex
@inproceedings{canh2019difference,
  title={Difference of convolution for deep compressive sensing},
  author={Canh, Thuong Nguyen and Jeon, Byeungwoo},
  booktitle={2019 IEEE International Conference on Image Processing (ICIP)},
  pages={2105--2109},
  year={2019},
  organization={IEEE}
}
```

