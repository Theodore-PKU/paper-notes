### Paper:

Dynamic MRI Reconstruction with Motion-Guided Network

### Author:

Qiaoying Huang, Dong Yang, Hui Qu, Jingru Yi, Pengxiang Wu, Dimitris Metaxas

### Year:

2019

### Notes:

阅读日期：2020.02.18

泛读。这篇文章研究的是动态 MRI 的重建。我认为这篇文章的创新点在于结合了计算视觉中的光流等概念，辅助重建。实际上这种辅助重建可以看成是一种后处理，或者修正。虽然作者把初始的目标函数同时包含了多个部分，实际上每个部分并不是同时进行的。简单说，先用一个 RNN 结构来重建图像，然后再用光流等和运动有关的信息来进一步修正图像。对于这个方法，本身对于光流等运动概念不熟悉，因此不细看了，和自己要思考的内容不相关。

作者给出的重建算法的目标是：

<img src="http://latex.codecogs.com/svg.latex? \underset{\left\{z_{t}, v_{t}, r_{t}\right\}}{\operatorname{argmin}} \mathcal{L}_{r e c}\left(\left\{z_{t}\right\}\right)+\eta \mathcal{L}_{m e}\left(\left\{v_{t}\right\}\right)+\zeta \mathcal{L}_{m c}\left(\left\{r_{t}\right\}\right)" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \mathcal{L}_{r e c}\left(\left\{z_{t}\right\}\right)=\sum_{t=1}^{T} \frac{1}{2}\left\|F_{u}\left(z_{t}\right)-y_{t}\right\|_{2}^{2}+\lambda R\left(z_{t}\right)" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \mathcal{L}_{m e}\left(\left\{v_{t}\right\}\right)=\sum_{t=1}^{T-1}\left\|\nabla z_{t}^{\top} v_{t}+\frac{\partial z_{t}}{\partial t}\right\|_{1}+\delta\left\|v_{t}\right\|_{1}" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \mathcal{L}_{m c}\left(\left\{r_{t}\right\}\right)=\sum_{t=1}^{T-1} \frac{1}{2}\left\|F_{u}\left(c_{t}\right)-y_{t}\right\|_{2}^{2}" border="0"/>

网络结构的第一个部分是：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200218191836.png"/>

第二个部分是运动信息有关的部分（没看懂）

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200218191618.png"/>

### Bibtex:

```latex
@article{huang2018dynamic,
  title={Dynamic MRI Reconstruction with Motion-Guided Network},
  author={Huang, Qiaoying and Yang, Dong and Qu, Hui and Yi, Jingru and Wu, Pengxiang and Metaxas, Dimitris N},
  year={2018}
}
```

