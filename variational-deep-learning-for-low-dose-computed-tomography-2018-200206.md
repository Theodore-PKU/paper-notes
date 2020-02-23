### Paper:

Variational Deep Learning for Low-dose Computed Tomography

### Author:

Erich Kobler, Matthew Muckley, Baiyu Chen, Florian Knoll, Kerstin Hammernik, Thomas Pock, Daniel Sodickson and Ricardo Otazo

### Year:

2018

### Notes:

阅读时间：2020.02.06

泛读。这篇文章研究的是 low-dose CT 的重建。CT 的 low-dose，有两种类型，一种是降低剂量，一种减少采样。这篇文章应用作者自己之前提出的 Variational network 模型来求解。没有什么参考意义。

正则项形如：

<img src="http://latex.codecogs.com/svg.latex? R_{c}(u)=\left\langle 1, \phi_{c}\left(K_{c} u ; W_{c}\right)\right\rangle" border="0"/>

对于低剂量 TCR，对应的目标函数是：

<img src="http://latex.codecogs.com/svg.latex? F_{T C R}:=\sum_{c=1}^{C} f_{D}^{c}(u)=R_{c}(u)+\frac{\lambda_{c}}{2}\left\|u-u_{0}\right\|_{2}^{2}" border="0"/>

对于欠采样 SCT，对应的目标函数是：

<img src="http://latex.codecogs.com/svg.latex? F_{S C T}:=\sum_{c=1}^{C} f_{R}^{c}(u)=R_{c}(u)+\frac{\lambda_{c}}{2}\|A u-d\|_{2}^{2}" border="0"/>

网络结构对应的是一个迭代算法：

<img src="http://latex.codecogs.com/svg.latex? u_{t}=u_{t-1}-\nabla f_{\{T C R, S C T\}}^{c(t)}\left(u_{t-1}\right)" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\nabla f_{T C R}^{c}(u)=K_{c}^{\top} \phi_{c}^{\prime}\left(K_{c} u ; W_{c}\right)+\lambda_{c}\left(u-u_{0}\right)} \\ {\nabla f_{S C T}^{c}(u)=K_{c}^{\top} \phi_{c}^{\prime}\left(K_{c} u ; W_{c}\right)+\lambda_{c} A^{\top}(A u-d)}\end{array}" border="0"/>

$\phi$ 使用一个线性插值来估计，$K_c$ 是卷积参数。

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

