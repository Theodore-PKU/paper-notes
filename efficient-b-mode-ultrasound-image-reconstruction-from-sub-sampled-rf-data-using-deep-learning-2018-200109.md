### Paper:

Efﬁcient B-mode Ultrasound Image Reconstruction from Sub-sampled RF Data using Deep Learning

### Author:

Yeo Hun Yoon, Shujaat Khan, Jaeyoung Huh, and Jong Chul Ye, Senior

### Year:

2018

### Notes:

阅读日期：2020.01.09

泛读。解决的问题的是超声图像重建，使用深度学习的方法。这篇文章和 Jong Chul Ye 之前的 framelet 的分析方法类似，因此网络结构就类似自编码结构。作者不在图像域上进行处理，而是在 RF data 上处理。作者认为这样可以增强泛化性。（RF data 似乎是和测量值有关）

由于超生的原理和数学模型不太了解，所以没有仔细看在 RF data 上处理的合理之处，可能 RF data 并不完全类似于 k-space 数据。

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-09%E4%B8%8A%E5%8D%8811.11.17.png)

### Bibtex:

```latex
@article{yoon2018efficient,
  title={Efficient b-mode ultrasound image reconstruction from sub-sampled rf data using deep learning},
  author={Yoon, Yeo Hun and Khan, Shujaat and Huh, Jaeyoung and Ye, Jong Chul},
  journal={IEEE transactions on medical imaging},
  volume={38},
  number={2},
  pages={325--336},
  year={2018},
  publisher={IEEE}
}
```