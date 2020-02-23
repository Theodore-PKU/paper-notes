### Paper:

Scan‐specific robust artificial‐neural‐networks for k‐space interpolation (RAKI) reconstruction: Database‐free deep learning for fast imaging

### Author:

Mehmet Akçakaya, Steen Moeller, Sebastian Weingärtner, Kâmil Uğurbil

### Year:

2018

### Notes:

阅读日期：2020.02.23

泛读。这篇文章研究的是多线圈 MRI 重建。这篇文章就是经常提到的 RAKI 算法了。方法说起来非常简单，类似于 GRAPPA 算法，只不过 GRAPPA 算法是线性的卷积核，这篇文章换成了三层卷积网络。GRAPPA 算法的想法就是用 ASC 区域的 kspace 来训练一个卷积核，这个卷积核和采样的区域经过卷积运算之后得到未采样的区域的 kspace。用图形表示（忽略不同的 coil，实际上是多个 coil 的 kspace 预测一个 coil 的某个未采样的点）

**| - - - | - - - | - - -|  **

竖线表示采样的，R = 4，- 表示未采样，m 对应的就是中间三个。

<img src="http://latex.codecogs.com/svg.latex? S_{j}\left(k_{x}, k_{y}-m \Delta k_{y}\right)  =\sum_{c=1}^{n_{c}} \sum_{b_{x}=-B_{x} b_{y}=-B_{y}}^{B_{y}} g_{j, m}\left(b_{x}, b_{y}, c\right) S_{c}\left(k_{x}-b_{x} \Delta k_{x}, k_{y}-R b_{y} \Delta k_{y}\right)" border="0"/>

注意上式中的 R，$B_y, B_x$ 就是参与卷积计算的范围，关于 y 方向乘上 R 是因为每 R line 才采样一次。RAKI 方法就是将 $g_{j,m}$ 换成了卷积网络，而且每个 coil 的 R-1 个输出结果变成了网络的 R-1 个通道，所以只要 coil 数量的网络就可以了。网络的损失函数好像没写。



<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200223210600.png"/>

作者称自己的方法是 scan-specific，而且 database free，原因在于是用每次采样的 kspace 的 ASC 区域重新训练网络，所以不需要其他的数据来训练。

### Bibtex:

```latex
@article{akccakaya2019scan,
  title={Scan-specific robust artificial-neural-networks for k-space interpolation (RAKI) reconstruction: Database-free deep learning for fast imaging},
  author={Ak{\c{c}}akaya, Mehmet and Moeller, Steen and Weing{\"a}rtner, Sebastian and U{\u{g}}urbil, K{\^a}mil},
  journal={Magnetic resonance in medicine},
  volume={81},
  number={1},
  pages={439--453},
  year={2019},
  publisher={Wiley Online Library}
}
```

