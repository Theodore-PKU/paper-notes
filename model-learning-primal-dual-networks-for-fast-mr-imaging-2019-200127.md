### Paper:

Model Learning: Primal Dual Networks for Fast MR imaging

### Author:

Jing Cheng , Haifeng Wang , Leslie Ying , Dong Liang

### Year:

2019

### Notes:

阅读时间：2020.01.27

泛读。这篇文章研究的 MRI 的重建，方法仍是 unroll 一个算法。作者此前的文章 Accelerating MR Imaging via Deep Chambolle-Pock Network 中的方法，在这篇文章中也提到了。不过在这篇文章中，作者有了更详细的讨论，主要是从最原始的 unroll 网络开始，一步一步放松原算法的限制，变成完全参数学习。这篇文章考虑的算法是 Chambolle-Pock (CP)。

如果设 $G(m)$ 为正则项，$m$ 是图像，则 CP 算法可以写为：

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{d_{n+1}=\frac{d_{n}+\sigma\left(A \bar{m}_{n}-f\right)}{1+\sigma}} \\ {m_{n+1}=p r o x_{\tau}[G]\left(m_{n}-\tau A^{*} d_{n+1}\right)} \\ {\bar{m}_{n+1}=m_{n+1}+\theta\left(m_{n+1}-m_{n}\right)}\end{array}\right." border="0"/>

这篇文章提出的第一个算法是 PDHS-CSnet，这里只有 $m$ 步用了神经网络：

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{d_{n+1}=\frac{d_{n}+\sigma\left(A \bar{m}_{n}-f\right)}{1+\sigma}} \\ {m_{n+1}=\Lambda\left(m_{n}-\tau A^{*} d_{n+1}\right)} \\ {\bar{m}_{n+1}=m_{n+1}+\theta\left(m_{n+1}-m_{n}\right)}\end{array}\right." border="0"/>

进一步放松 data fidelity（也就是 $d$ 步），得到 CP-net，这里用了两个神经网络：

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{d_{n+1}=\Gamma\left(d_{n}+\sigma A \bar{m}_{n}, f\right)} \\ {m_{n+1}=\Lambda\left(m_{n}-\tau A^{*} d_{n+1}\right)} \\ {\bar{m}_{n+1}=m_{n+1}+\theta\left(m_{n+1}-m_{n}\right)}\end{array}\right." border="0"/>

进一步将加速步省略，得到 PD-net：

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{d_{n+1}=\Gamma\left(d_{n}, A m_{n}, f\right)} \\ {m_{n+1}=\Lambda\left(m_{n}, A^{*} d_{n+1}\right)}\end{array}\right." border="0"/>

使用的网络结构都是 res net，注意这里的输入通道数的变化取决于函数输入有几个变量，每个变量都是一个复数值，所以对应两个通道。三种网络的示意图

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-27%E4%B8%8A%E5%8D%889.26.15.png" width="70%"/>

关于实验，作者有一个表述：当训练数据越来越多的时候，PD-net 这个完全没有限制的模型表现得最好，换句话说，对约束的relax越多，在数据充分的情况下，效果更好。

### Bibtex:

```latex
@inproceedings{cheng2019model,
  title={Model learning: Primal dual networks for fast MR imaging},
  author={Cheng, Jing and Wang, Haifeng and Ying, Leslie and Liang, Dong},
  booktitle={International Conference on Medical Image Computing and Computer-Assisted Intervention},
  pages={21--29},
  year={2019},
  organization={Springer}
}
```

