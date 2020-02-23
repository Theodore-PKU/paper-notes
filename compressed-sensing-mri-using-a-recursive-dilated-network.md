### Paper:

Compressed Sensing MRI Using a Recursive Dilated Network

### Author:

Liyan Sun, Zhiwen Fan, Yue Huang, Xinghao Ding,  John Paisley

### Year:

2018

### Notes:

这篇文章是用一个卷积网络来重建MRI图像。作者的关注点是如何减少网络参数的同时也能保持好的重建效果，因此主要内容是关于网络结构的设计。整体思路还是将 zero-fill 的图像映射为高质量图像。

作者减少参数量的主要想法是使用共享参数，即每个卷积block用的是相同的卷积和参数。这是整体框架，在此基础上，作者考虑了残差学习（两种，除了一般形式，作者考虑的 plus 操作是将最开始的图像加进去，见下图）、data fidelity（也就是填补 kspace 系数）、空洞卷积（dilated convolution，增加多尺度的信息）。

整体框架（循环单元，就像RNN？）
$$
\mathrm{CNN}: \mathrm{x}^{\mathrm{t}}=\mathrm{f}\left(\mathrm{x}^{\mathrm{t}-1}\right), \quad \mathrm{x}^{\mathrm{t}+1}=\mathrm{f}\left(\mathrm{x}^{\mathrm{t}}\right)\\
\text { ResNet: } \quad \mathrm{x}^{\mathrm{t}}=\mathrm{f}\left(\mathrm{x}^{\mathrm{t}-1}\right)+\mathrm{x}^{\mathrm{t}-1}, \quad \mathrm{x}^{\mathrm{t}+1}=\mathrm{f}\left(\mathrm{x}^{\mathrm{t}}\right)+\mathrm{x}^{\mathrm{t}}\\
\mathrm{RDN}: \mathrm{x}^{\mathrm{t}}=\mathrm{f}\left(\mathrm{x}^{\mathrm{t}-1}\right)+\mathrm{x}^{1}, \quad \mathrm{x}^{\mathrm{t}+1}=\mathrm{f}\left(\mathrm{x}^{\mathrm{t}}\right)+\mathrm{x}^{1}
$$
<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-26%E4%B8%8B%E5%8D%881.24.51.png" style="zoom:50%;" />

空洞卷积：作者用的是固定的空洞大小。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-26%E4%B8%8B%E5%8D%881.24.57.png" style="zoom: 50%;" />

data fidelity:
$$
\hat{x}=\underset{x}{\arg \min } \frac{\lambda}{2}\left\|F_{u} x-y\right\|_{2}^{2}+\left\|x-x_{i n}\right\|_{2}^{2}\\
\hat{x}=F^{H} \frac{\lambda F F_{u}^{H} y+F x_{i n}}{\lambda F F_{u}^{H} F_{u} F^{H}+I}
$$
最终的整体框架：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-26%E4%B8%8B%E5%8D%881.25.03.png" style="zoom:50%;" />

MRI 伪影的全局特征：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-26%E4%B8%8B%E5%8D%881.24.44.png" style="zoom:50%;" />

作者做的实验也比较多，包括：RDN 和残差网络的差别，是否有空洞卷积的差别，和其他方法的对比，循环层数、空洞大小等。

### Bibtex:

```latex
@inproceedings{sun2018compressed,
  title={Compressed sensing MRI using a recursive dilated network},
  author={Sun, Liyan and Fan, Zhiwen and Huang, Yue and Ding, Xinghao and Paisley, John},
  booktitle={Thirty-Second AAAI Conference on Artificial Intelligence},
  year={2018}
}
```