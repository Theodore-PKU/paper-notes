### Paper:

A Divide-and-Conquer Approach to Compressed Sensing MRI

### Author:

Liyan Sun, Zhiwen Fan, Xinghao Ding, Congbo Cai, Yue Huang, John Paisley

### Year:

2019

### Notes:

阅读日期：2020.02.13

泛读。这篇文章研究的是 MRI 重建。作者在这篇文章中提出的是一种重建的框架，而是不是具体的重建方法。简单的说，作者的方法就是先用 filter 把要重建的图像分成几个部分，各自单独用某一重建方法重建，然后再合并起来。作者的动机来源于这样的观察：一般的方法在重建时对于高频和低频的误差是一视同仁的，但是低频的能量（这里的能量就是用 kspace 系数的绝对值衡量）比高频高很多，因此实际上会更侧重低频部分。作者做了一个实验，对重建结果在 kspace 域调节 kspace 的系数，使得 PSNR 不变，但是 SSIM 提高了。这种调节的另一个观察是 kspace 的相对能量的误差会变得更平均，而前者往往是高频部分的相对能量误差高很多。关于作者的方法，具体来说，划分用的 filter 需要考虑的是完备性（包含所有的系数），lossless，可以有冗余，也可以没有冗余。作者考虑了两种，一种是 vertical and horizontal，一种是高斯。重建的方法不属于这篇文章的讨论范围。关于合并，如果是无冗余的情形，可以直接全部加起来，另一种做法则是考虑不同的区域的权重，使用 Tikhonov regularization method 的方式。作者的实验结果表明，这种框架比原来的重建效果更好。

整体的框架：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200213112952.png" width="70%"/>

能量相对误差的实验

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200213113011.png" width="70%"/>

filter 就是在图像域做卷积，或者傅立叶域点点乘上一个矩阵。可以直接使用 under-sample 的 kspace.

<img src="http://latex.codecogs.com/svg.latex? X_{f_{i}}=X_{f} * H_{i}" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? Y_{f_{i}}=Y_{f} \odot \widehat{H}_{i}" border="0"/>

两种 filter：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200213113328.png"/>

合成部分，Tikhonov regularization method:

<img src="http://latex.codecogs.com/svg.latex? x=\arg \min _{x} \sum_{i=1}^{n} \lambda_{i}\left\|x_{i}-\widetilde{H}_{i} x\right\|_{2}^{2}" border="0"/>

最终算法：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200213113429.png"/>

### Bibtex:

```latex
@article{sun2019divide,
  title={A divide-and-conquer approach to compressed sensing MRI},
  author={Sun, Liyan and Fan, Zhiwen and Ding, Xinghao and Cai, Congbo and Huang, Yue and Paisley, John},
  journal={Magnetic resonance imaging},
  volume={63},
  pages={37--48},
  year={2019},
  publisher={Elsevier}
}
```

