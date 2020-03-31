### Paper:

Dynamic PET Image Denoising Using Deep Convolutional Neural Networks Without Prior Training Datasets

### Author:

Fumio Hashimoto, Hiroyuki Ohba, Kibo Ote, Atsushi Teramoto, and Hideo Tsukada

### Year:

2019

### Notes:

泛读。这篇文章研究的是动态 PET 的重建或者去噪，如果是 low-dose 的情形，也是一直重建。使用的方法是 DIP，属于无监督方法。具体的做法和 DIP 几乎没有区别，网络的输入是 static PET 图像，输出是动态 PET 图像。label 是模糊的动态 PET 重建图像。网络结构是 3D U-net。损失函数是 2 范数。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200331151021.png"/>

### Bibtex:

```latex
@article{hashimoto2019dynamic,
  title={Dynamic PET image denoising using deep convolutional neural networks without prior training datasets},
  author={Hashimoto, Fumio and Ohba, Hiroyuki and Ote, Kibo and Teramoto, Atsushi and Tsukada, Hideo},
  journal={IEEE Access},
  volume={7},
  pages={96594--96603},
  year={2019},
  publisher={IEEE}
}
```

### 其他

PET image reconstruction using deep image prior 这篇文章以前看过了，但是属于 PET 部分，综述需要写进来。