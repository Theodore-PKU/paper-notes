### Paper:

3D BPConvNet to reconstruct parallel MRI

### Author:

Kyong Hwan Jin and Michael Unser

### Year:

2018

### Notes:

阅读日期：2020.02.11

泛读。这篇文章研究的是 3D parallel MRI 重建。使用的方法仍然是 U-Net。首先，对于 mulit-coil，作者通过 SSoS 计算获得 single channel 的图像，然后经过小波变换（haar）得到网络的输入，网络结构来源于作者此前提出的 FBPconvNet，就是 U-net 骨架的网络，有一些小变化，比如 residual connection 等。因为 3D 的计算量非常大，所以作者用多卡训练的方式。

整体的网络结构是：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200211211711.png"/>

对于 full-sample 的数据，SSoS 算子是：

<img src="http://latex.codecogs.com/svg.latex? S(f(\mathbf{x}))=\sqrt{\left|f_{1}(\mathbf{x})\right|^{2}+\left|f_{2}(\mathbf{x})\right|^{2}+\cdots+\left|f_{C}(\mathbf{x})\right|^{2}}" border="0"/>

对于 under-sampled 的数据，SSoS 算子是：

<img src="http://latex.codecogs.com/svg.latex? S\left(f_{d}(\mathbf{x})\right)=\sqrt{\left|M(\mathbf{x}) * f_{1}(\mathbf{x})\right|^{2}+\cdots+\left|M(\mathbf{x}) * f_{C}(\mathbf{x})\right|^{2}}" border="0"/>

文中说 $M(x)$ is the kernel associated with the sampling mask. 但是没有理解具体的作用是什么。上述计算中的 $f_i(x)$ 表示的就是不同的 coil 经过傅立叶逆变换计算的图像。

### Bibtex:

```latex
@inproceedings{jin20183d,
  title={3D BBPConvNet to reconstruct parallel MRI},
  author={Jin, Kyong Hwan and Unser, Michael},
  booktitle={2018 IEEE 15th International Symposium on Biomedical Imaging (ISBI 2018)},
  pages={361--364},
  year={2018},
  organization={IEEE}
}
```

