### Paper:

Adaptive Deep Dictionary Learning for MRI Reconstruction

### Author:

D. John Lewis, Vanika Singhal, and Angshul Majumdar

### Year:

2018

### Notes:

阅读日期：2020.01.15

泛读。这篇文章研究的是MRI的重建。作者使用的方法是深度字典学习。作者沿用了字典学习的框架，只是将字典的层数加深，仍然是一个传统的计算方式，并且没有数据集训练的过程，完全是自适应的重建算法。这篇文章应该说没什么营养。

字典学习的数学形式：

<img src="http://latex.codecogs.com/svg.latex? \min _{x, D, Z}\|y-R F x\|_{2}^{2}+\lambda\left(\sum_{i}\left\|P_{i} x-D z_{i}\right\|_{2}^{2}+\gamma\left\|z_{i}\right\|_{1}\right)" border="0"/>

深度字典学习的形式：

<img src="http://latex.codecogs.com/svg.latex? \min _{x, D_{1}, D_{2}, D_{3}, Z}\|y-A x\|_{2}^{2}+\lambda\left(\sum_{i}\left\|P_{i} x-D_{1} \varphi\left(D_{2} \varphi\left(D_{3}z_{i}\right)\right)\right\|_{2}^{2}+\gamma\left\|z_{i}\right\|_{1}\right)" border="0"/>

这里作者用了三层的字典，也可以继续加深。

重建算法是ADMM算法：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned}\min _{x, D_{1}, D_{2}, D_{3}, Z, Z_{1}, Z_{2}}\|y-A x\|_{2}^{2}+\lambda\left(\sum_{i}\left\|P_{i} x-D_{1} z_{1, i}\right\|_{2}^{2}+\gamma\left\|z_{i}\right\|_{1}\right)+\mu\left(\left\|Z_{1}-\varphi\left(D_{2} Z_{2}\right)\right\|_{F}^{2}+\left\|Z_{2}-\varphi\left(D_{3} Z\right)\right\|_{F}^{2}\right)\end{aligned}" border="0"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-15%E4%B8%8B%E5%8D%885.02.47.png"/>

其中 P(1)~P(6) 是简单的线性最小二乘，P(7) 用 ISTA 算法求解。

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned}&\min _{x, D_{1}, D_{2}, D_{3}, Z, Z_{1}, Z_{2}}\|y-A x\|_{2}^{2}+\lambda\left(\sum_{i}\left\|P_{i} x-D_{1} z_{1, i}\right\|_{2}^{2}+\gamma\left\|z_{i}\right\|_{1}\right)\\&+\mu\left(\left\|Z_{1}-\varphi\left(D_{2} Z_{2}\right)\right\|_{F}^{2}+\left\|Z_{2}-\varphi\left(D_{3} Z\right)\right\|_{F}^{2}\right)\end{aligned}" border="0"/>

### Bibtex:

```latex
@inproceedings{lewis2018adaptive,
  title={Adaptive Deep Dictionary Learning for MRI Reconstruction},
  author={Lewis, D John and Singhal, Vanika and Majumdar, Angshul},
  booktitle={International Conference on Neural Information Processing},
  pages={3--11},
  year={2018},
  organization={Springer}
}
```

