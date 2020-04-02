### Paper:

Deep Learning for PET Imaging: from Denoising to Learned Primal-Dual Reconstruction

### Author:

Alessandro Guazzo

### Year:

2020

### Notes:

泛读。这篇文章研究的是 PET 重建（low-dose），使用的方法有好几个。

第一种方法是 denoising 类型，也就CNN直接重建，将传统算法的重建结果作为网络输入，输出是高质量图像。这种方法的网络选择 U-net 或没有 skip connection 的 U-net。实验结果是前者好一些。

第二种方法是多个 U-net 迭代，但是第二个 U-net 开始，输入就不只是图像了。作者称之为 learn update 方法，第一个网络的输入是 R(s), 使用 forward model 的测量矩阵的转置计算的初步重建。后续网络的输入则是前一个网络重建结果的 sinogram 和原始 sinogram 的差值的 $R(s^{i-1} - s)$ 和前一个网络的重建结果。最终的输出是一个 residual 结构。在此基础上作者改进的版本是将此前的重建中间值也包含到输入中来。

第三种方法是 Learned Primal-Dual，和 LU 方法类似，只不过除了 image 上的投影 U-net，每个 iteration 还包括 sinogram 上的 U-net。顺序是 sino-image-sino-image。sinogram 和 image 之间通过测量矩阵和测量矩阵的转置进行转换。同样包括了重建的中间值。比较奇怪的是用测量矩阵的转置的结果作为 image U-net 的输入，感觉不一定有多好的效果（在 phantom 上的实验确实表现得还不如 LU 方法）。

关于网络的训练，主要是 checkpoint 策略（多段训练，找最好的网络）和 progressive training。后者就是先训练前一部分网络，然后再加入新的 iteration 的网络进行训练。

在 PET 重建中，比较难以实现的部分主要还是 DC 吧。在这篇文章中，LU 和 LPD 中都没有 DC 的存在。这两个方法总体上只能当作是多个 CNN 投影。测量矩阵的转置其实并不是一个很好的 DC 的表示。

给出几个网络示意图。

LU

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200402105552.png"/>

LUM

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200402105557.png"/>

LPD

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200402105605.png"/>

### Bibtex:

```latex
@misc{guazzo2020deep,
  title={Deep Learning for PET Imaging: From Denoising to Learned Primal-Dual Reconstruction},
  author={Guazzo, Alessandro},
  year={2020}
}
```

### 其他

