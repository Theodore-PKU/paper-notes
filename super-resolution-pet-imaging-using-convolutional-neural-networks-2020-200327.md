### Paper:

Super-Resolution PET Imaging Using Convolutional Neural Networks

### Author:

Tzu-An Song, Samadrita R. Chowdhury, Fan Yang, Joyita Dutta

### Year:

2020

### Notes:

泛读。这篇文章研究的是 PET 的超分辨。主要的做法是利用 SR 领域的方法，结合 MRI 的高清图像信息。这篇笔记主要提一个点：关于信息融合，作者表示在网络的  lower level 部分，使用不同的 kernel 比较好，然后在网络的 higher level 部分再融合起来。关于信息融合，也是一个值得使用空间划分的内容来思考的。这篇文章很大的篇幅在于实验，作者设计了各种类型的实验，包括网络的深浅、输入的组合选择等。不过这些内容不是我关心的，所以就不细看了。

大致的网络结构（其中有两个 input 不清楚是什么内容）：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200327215820.png" width="50%"/>

### Bibtex:

```latex
@article{song2020super,
  title={Super-resolution PET imaging using convolutional neural networks},
  author={Song, Tzu-An and Chowdhury, Samadrita Roy and Yang, Fan and Dutta, Joyita},
  journal={IEEE Transactions on Computational Imaging},
  volume={6},
  pages={518--528},
  year={2020},
  publisher={IEEE}
}
```

### 其他

