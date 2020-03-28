### Paper:

Deep Auto-context Convolutional Neural Networks for Standard-Dose PET Image Estimation from Low-Dose PET/MRI

### Author:

Lei Xiang, Yu Qiao, Dong Nie, Le An, Qian Wang, and Dinggang Shen

### Year:

2017

### Notes:

泛读。这篇文章比较简单，研究的是 PET 重建。具体方法是用 CNN 将 low-dose PET 投影到 standard-dose PET，MRI 图像作为额外信息也是 CNN 的输入。CNN 很简单，只有四层。作者因此考虑将三个 CNN 重叠起来，前一个 CNN 的输出也是后一个 CNN 的输入，构成 stack 的形式。同时 MRI 是每一个 CNN 的输入。整个网络处理的是 2D 数据，也就是将 3D 的图像用 slice by slice 的方式处理。网络的输入和输出都是 patch，由于卷积导致 size 变小，所以作者的输入 patch 的大小和输出的 patch 大小不同。损失函数是 3 个 CNN 的输出的 2 范数和网络参数的正则项。

网络结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200328195937.png"/>

### Bibtex:

```latex
@article{xiang2017deep,
  title={Deep auto-context convolutional neural networks for standard-dose PET image estimation from low-dose PET/MRI},
  author={Xiang, Lei and Qiao, Yu and Nie, Dong and An, Le and Lin, Weili and Wang, Qian and Shen, Dinggang},
  journal={Neurocomputing},
  volume={267},
  pages={406--416},
  year={2017},
  publisher={Elsevier}
}
```

### 其他

