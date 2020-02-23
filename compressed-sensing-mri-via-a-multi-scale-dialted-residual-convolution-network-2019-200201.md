### Paper:

Compressed sensing MRI via a multi-scale dilated residual convolution network

### Author:

Yuxiang Dai, Peixian Zhuang

### Year:

2019

### Notes:

阅读时间：2020.02.01

泛读。这篇文章研究的是 MRI 重建，作者使用的方法是深度学习，直接将 zero-fill 重建的图像作为网络的输入，输出是重建好的图像。作者使用的网络结构包含了 global residual，local residual，dilated convolution 和 multi-scale 信息融合（应该就是 dense concatenation）。损失函数是图像域 2 范数。作者实验还算多，关于网络结构本身的对比实验和其他方法的对比实验，采样方式也使用了三种。作者还尝试了用在 noisy 和超分辨两种情形。

流程图非常简单：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-01%E4%B8%8A%E5%8D%881.58.10.png"/>

网络结构示意图如下，在下图中，可以看到 dilated convolution，不同的 residual bypass connection 和 block 内部的 dense concatenation。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-01%E4%B8%8A%E5%8D%881.58.28.png"/>

### Bibtex:

```latex
@article{dai2019compressed,
  title={Compressed sensing MRI via a multi-scale dilated residual convolution network},
  author={Dai, Yuxiang and Zhuang, Peixian},
  journal={Magnetic resonance imaging},
  volume={63},
  pages={93--104},
  year={2019},
  publisher={Elsevier}
}
```

