### Paper:

Variational Deep Learning for Low-dose Computed Tomography

### Author:

Erich Kobler, Matthew Muckley, Baiyu Chen, Florian Knoll, Kerstin Hammernik, Thomas Pock, Daniel Sodickson and Ricardo Otazo

### Year:

2018

### Notes:

阅读日期：2020.02.28

泛读。这篇文章其实非常简单，和 VN 的方法一模一样，只不过换成了 CT。对于 low-dose CT，有两种方式，一种是 current-reduction，也就是剂量低 (TCR)，一种是 sparse view，也就是 undersample (SCT)。VN 都可以用于这两种情况，只不过 data consistency 不一样了。

因为和 VN 方法一模一样，所以就简单地回顾一下。

正则项使用的还是 fields of experts：

<img src="http://latex.codecogs.com/svg.latex? R_{c}(u)=\left\langle 1, \phi_{c}\left(K_{c} u ; W_{c}\right)\right\rangle" border="0"/>

两个目标函数：

<img src="http://latex.codecogs.com/svg.latex? F_{T C R}:=\sum_{c=1}^{C} f_{D}^{c}(u)=R_{c}(u)+\frac{\lambda_{c}}{2}\left\|u-u_{0}\right\|_{2}^{2}" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? F_{S C T}:=\sum_{c=1}^{C} f_{R}^{c}(u)=R_{c}(u)+\frac{\lambda_{c}}{2}\|A u-d\|_{2}^{2}" border="0"/>

网络展开的公式是一阶梯度的迭代：

<img src="http://latex.codecogs.com/svg.latex? u_{t}=u_{t-1}-\nabla f_{\{T C R, S C T\}}^{c(t)}\left(u_{t-1}\right)" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}\nabla f_{T C R}^{c}(u)=K_{c}^{\top} \phi_{c}^{\prime}\left(K_{c} u ; W_{c}\right)+\lambda_{c}\left(u-u_{0}\right) \\ \nabla f_{S C T}^{c}(u)=K_{c}^{\top} \phi_{c}^{\prime}\left(K_{c} u ; W_{c}\right)+\lambda_{c} A^{\top}(A u-d)\end{array}" border="0"/>

$\phi_c$ 的参数化方法还是线性差值。

### Bibtex:

```latex
@inproceedings{kobler2018variational,
  title={Variational deep learning for low-dose computed tomography},
  author={Kobler, Erich and Muckley, Matthew and Chen, Baiyu and Knoll, Florian and Hammernik, Kerstin and Pock, Thomas and Sodickson, Daniel and Otazo, Ricardo},
  booktitle={2018 IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP)},
  pages={6687--6691},
  year={2018},
  organization={IEEE}
}
```

