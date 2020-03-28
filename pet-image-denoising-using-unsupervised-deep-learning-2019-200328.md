### Paper:

PET image denoising using unsupervised deep learning

### Author:

Jianan Cui, Kuang Gong, Ning Guo, Chenxi Wu, Xiaxia Meng, Kyungsang Kim, Kun Zheng, Zhifang Wu, Liping Fu, Baixuan Xu, Zhaohui Zhu, Jiahe Tian, Huafeng Liu and Quanzheng Li

### Year:

2019

### Notes:

泛读。这篇文章研究的是 PET 重建，使用的方法是 DIP 无监督方法。具体就是用 3D U-net 作为生成网络，输入图像是 CT 或 MRI 的相关图像（这是所谓的 prior informaiton），输出是 high-dose PET 图像。目标函数就是和真实的 low-dose PET 图像进行比较。DIP 的框架下，用 L-BFGS 算法对网络参数进行优化。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200328141621.png"/>

### Bibtex:

```latex
@article{cui2019pet,
  title={PET image denoising using unsupervised deep learning},
  author={Cui, Jianan and Gong, Kuang and Guo, Ning and Wu, Chenxi and Meng, Xiaxia and Kim, Kyungsang and Zheng, Kun and Wu, Zhifang and Fu, Liping and Xu, Baixuan and others},
  journal={European journal of nuclear medicine and molecular imaging},
  volume={46},
  number={13},
  pages={2780--2789},
  year={2019},
  publisher={Springer}
}
```

### 其他

Chen KT, Gong E, de Carvalho Macruz FB, Xu J, Boumis A, Khalighi M, et al. Ultra–Low-Dose 18 FFlorbetaben Amyloid PET Imaging Using Deep Learning with Multi-Contrast MRI Inputs. Radiology. 2019;290:649–56. Available from: https://doi.org/10.1148/radiol.2018180940

Xiang L, Qiao Y, Nie D, An L, Wang Q, Shen D. Deep Auto-context Convolutional Neural Networks for Standard-Dose PET Image Estimation from Low-Dose PET/MRI. Neurocomputing. 2018;406–16. Available from: oi.org/10.1016/j.neucom.2017.06.048