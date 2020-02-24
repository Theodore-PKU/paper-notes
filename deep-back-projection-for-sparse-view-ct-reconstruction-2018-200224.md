### Paper:

Deep Back Projection for Sparse-view CT Reconstruction

### Author:

Dong Hye Ye, Gregery T. Buzzard, Max Ruby, and Charles A. Bouman

### Year:

2018

### Notes:

阅读日期：2020.02.24

泛读。这篇文章研究的是 CT 重建。方法就是 CNN，不过网络的输入不是 sinogram，也不是 FBP 算法重建的初步结果，而是经过所谓的 back projection 变换。sinogram 可以看成是不同角度，不同view 的采样。作者把所有 view 在不同角度的采样变成一条直线，方向就是该角度，所以有几个 view，就可以构成几个图像，然后把这些图像叠成一个 tensor，作为网络的输入。网络结构是一个 15 层的 CNN，输出就是重建的图像。

<img src="https://cdn.mathpix.com/snip/images/pG_pcUjCVvBdNUDfPJUDqGxd3o-wfBtvjI939Et_Sig.original.fullsize.png" />

一个 back projection 的例子：

<img src="https://cdn.mathpix.com/snip/images/iUAeUsEy0cy0KM-TtiS5XDEf3qu52MsMgxZ9Xra-eLc.original.fullsize.png" />

### Bibtex:

```latex
@inproceedings{ye2018deep,
  title={Deep back projection for sparse-view CT reconstruction},
  author={Ye, Dong Hye and Buzzard, Gregery T and Ruby, Max and Bouman, Charles A},
  booktitle={2018 IEEE Global Conference on Signal and Information Processing (GlobalSIP)},
  pages={1--5},
  year={2018},
  organization={IEEE}
}
```

