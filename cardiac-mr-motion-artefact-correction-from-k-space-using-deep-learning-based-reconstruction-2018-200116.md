### Paper:

Cardiac MR Motion Artefact Correction from K-space using Deep Learning-based Reconstruction

### Author:

Ilkay Oksuz, James Clough, Aurelien Bustin, Gastao Cruz, Claudia Prieto, Rene Botnar, Daniel Rueckert, Julia A. Schnabel, Andrew P. King

### Year:

2018

### Notes:

阅读日期：2020.01.16

泛读。这篇文章研究的是如何去除 cardiac MRI 图像的 motion 伪影。使用的方法是 kspace to image 的 GAN。方法很简单，没有什么参考价值。网络结构很简单，先是两个全连接层，然后用卷积和反卷积，最后是一个 1x1 卷积层。判别网络也很简单。网络结构示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-16%E4%B8%8B%E5%8D%888.19.57.png"/>

训练时，作者先用 ImageNet 的kspace 做预训练，然后再用 MRI 数据训练。另外作者提到了一个 regularizer，但是没有说明具体内容。具有 motion 伪影的数据的产生方法是：假设是笛卡尔采样。对 cartesian sampling lines 换成其他的 cardiac phase 的sampling lines

### Bibtex:

```latex
@inproceedings{oksuz2018cardiac,
  title={Cardiac mr motion artefact correction from k-space using deep learning-based reconstruction},
  author={Oksuz, Ilkay and Clough, James and Bustin, Aurelien and Cruz, Gastao and Prieto, Claudia and Botnar, Rene and Rueckert, Daniel and Schnabel, Julia A and King, Andrew P},
  booktitle={International Workshop on Machine Learning for Medical Image Reconstruction},
  pages={21--29},
  year={2018},
  organization={Springer}
}
```

