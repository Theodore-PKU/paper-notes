### Paper:

Deep Residual Dense U-Net for Resolution Enhancement in Accelerated MRI Acquisition

### Author:

Pak Lun Kevin Din, Zhiqiang Li, Yuxiang Zhou, and Baoxin Li

### Year:

2019

### Notes:

阅读时间：2020.02.03

泛读。这篇文章研究的是 MRI 重建，使用的方法说起来非常简单，基本框架是 U-Net 的 image process 形式。作者的方法主要的特点是在 U-net 的 skip 部分，加了一个 dense-residual 的卷积结构，以及在损失函数中加入了 kspace 的1 范数损失函数。这两个点没有什么特别稀奇的，单纯的网络结构的微调和 loss 的增加。参考意义不大。

网络结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-03%E4%B8%8B%E5%8D%8810.01.07.png"/>

skip 部分的改进：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-03%E4%B8%8B%E5%8D%8810.01.14.png" width="50%"/>

### Bibtex:

```latex
@inproceedings{ding2019deep,
  title={Deep residual dense U-Net for resolution enhancement in accelerated MRI acquisition},
  author={Ding, Pak Lun Kevin and Li, Zhiqiang and Zhou, Yuxiang and Li, Baoxin},
  booktitle={Medical Imaging 2019: Image Processing},
  volume={10949},
  pages={109490F},
  year={2019},
  organization={International Society for Optics and Photonics}
}
```

