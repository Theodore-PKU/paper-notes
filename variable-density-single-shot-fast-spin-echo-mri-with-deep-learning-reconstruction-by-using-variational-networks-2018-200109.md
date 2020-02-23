### Paper:

Variable-Density Single-Shot Fast Spin-Echo MRI with Deep Learning Reconstruction by Using Variational Networks

### Author:

Feiyu Chen, Valentina Taviani, Itzik Malkiel, Joseph Y. Cheng,  Jonathan I. Tamir, Jamil Shaikh, Stephanie T. Chang, Christopher J. Hardy, John M. Pauly, Shreyas S. Vasanawala

### Year:

2018

### Notes:

阅读日期：2020.01.09

泛读。多线圈MRI重建（注：题目里的 spin echo 等表示的是MRI序列）。作者使用的方法是 variational networks，和最早提出 variational networks 的那篇文章似乎差不多。网络结构是 recursive blocks，每个block 包含 data consistency 和 regularization。

block 的数学表达式可以写为

<div align=center>
    <img src="http://latex.codecogs.com/svg.latex? u_{t+1}=u_{t}-\lambda_{t} A\left(A^{*} u_{t}-y\right)-\sum_{i=1}^{K} k_{i, t}^{\top} f_{i, t}\left(k_{i, t} u_{t}\right)"  border="0"/>
</div>

其中，第三项表示 regularization 项，<img src="http://latex.codecogs.com/svg.latex? k_{i,t}"> 表示卷积运算，每个block包含 K 个不同的卷积（11x11），第三项的计算可以看成是卷积+shrinkage+卷积转置。<img src="http://latex.codecogs.com/svg.latex? f_{i,t}"> 表示高斯径向基函数（作为可学习的激活函数）：

<div align=center>
    <img src="http://latex.codecogs.com/svg.latex?f_{i, t}(x)=\sum_{j=1}^{N} w_{i, t, j} \exp \left(-\frac{\left(x-\mu_{j}\right)^{2}}{2 \sigma^{2}}\right)" border="0" align="center"/>
</div>

<div align=center>
    <img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-09%E4%B8%8B%E5%8D%881.43.24.png" width="50%" />
</div>

损失函数为 mse，sgd的优化器。作者做完实验后，用统计上的方法验证 improve 是否有效。结果是在 perceived signal-to-noise ratio 和 sharpness 上有明显变化。

> 和本文无关，想到一个关于 DIP 的分析方式：逐层分析，分析优化特性，特别是网络参数随机初始化，只更新输入。相反的也许也可以。可能可以看出为什么这两种更新方法效果不一样，也许可以对卷积神经网络和刻画图像分布有一些关系。

### Bibtex:

```latex
@article{chen2018variable,
  title={Variable-density single-shot fast spin-echo MRI with deep learning reconstruction by using variational networks},
  author={Chen, Feiyu and Taviani, Valentina and Malkiel, Itzik and Cheng, Joseph Y and Tamir, Jonathan I and Shaikh, Jamil and Chang, Stephanie T and Hardy, Christopher J and Pauly, John M and Vasanawala, Shreyas S},
  journal={Radiology},
  volume={289},
  number={2},
  pages={366--373},
  year={2018},
  publisher={Radiological Society of North America}
}
```