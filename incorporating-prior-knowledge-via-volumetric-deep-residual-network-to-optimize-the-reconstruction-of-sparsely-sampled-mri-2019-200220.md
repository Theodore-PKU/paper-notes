### Paper:

Incorporating prior knowledge via volumetric deep residual network to optimize the reconstruction of sparsely sampled MRI

### Author:

Yan Wu, Yajun Ma b , Dante Pietro Capaldi a , Jing Liu c , Wei Zhao a , Jiang Du b , Lei Xing

### Year:

2019

### Notes:

阅读日期：2020.02.20

泛读。这篇文章研究的是 MRI 重建，用的方法太简单了，主要内容是用一个网络直接将 zero-fill 图像重建为高质量图像。在测试的时候对最终图像再加一个 data consistency enhance。没有啥特别的。总结这篇文章的几个特点：1.  Hierachical network —U-net； 2. Dense local connect — 局部的结构；3. Global shortcut — residual learning； 4. DC enhance — 最后一层 DC 提升；5. 3d 的输入，利用3d信息。损失函数也就是 2 范数。

随便上点图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200220111659.png"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200220111727.png" width="80%"/>



### Bibtex:

```latex
@article{wu2019incorporating,
  title={Incorporating prior knowledge via volumetric deep residual network to optimize the reconstruction of sparsely sampled MRI},
  author={Wu, Yan and Ma, Yajun and Capaldi, Dante Pietro and Liu, Jing and Zhao, Wei and Du, Jiang and Xing, Lei},
  journal={Magnetic resonance imaging},
  year={2019},
  publisher={Elsevier}
}
```

