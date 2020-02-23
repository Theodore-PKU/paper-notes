### Paper:

Learned Full-Sampling Reconstruction

### Author:

Weilin Cheng, Yu Wang, Ying Chi, Xuansong Xie, and Yuping Duan

### Year:

2019

### Notes:

阅读日期：2020.02.19

泛读。这篇文章研究的是 sparse view CT 的重建。思想还是 unroll 传统算法，然后用 CNN 替代 proximal 算子。不过这篇文章的传统算法的目标函数和一般的不太一样，多了一项（而且无法理解为什么要多这一项），另外用 ADMM 求解后得到的是 4 个 proximal 算子。作者把这些 proximal 算子都用 3 层的 CNN 替换了。对应的网络结构就是连接好输入即可。

目标函数：

<img src="http://latex.codecogs.com/svg.latex? \min _{u \in X, f \in Z, \tilde{u} \in X} \mathcal{D}\left(A_{S} \tilde{u}, g\right)+\mathcal{R}(\tilde{u})+\mathcal{F}\left(A_{F} u, f\right)+\frac{1}{2 r}\|\tilde{u}-u\|_{X}^{2}" border="0"/>

这里的前两项和一般的做法相同。第三项是新增的，第四项是变量替换之后补充的。$A_F$ 论文中说是完整采样情况下的测量矩阵。

> 作者仍然用了 data fidelity 和 prior，只是多了一项，我认为没有意义的项。不明白为什么要这样做。实验结果还比其他算法都要好。

作者将目标函数转换成：

<img src="http://latex.codecogs.com/svg.latex? \min _{u \in X, f \in Z, \tilde{u} \in X} \max _{p \in Y}\left\langle A_{S} \tilde{u}, p\right\rangle-\mathcal{D}^{*}(p, g)+\mathcal{R}(\tilde{u})+\mathcal{F}\left(A_{F} u, f\right)+\frac{1}{2 r}\|\tilde{u}-u\|_{X}^{2}" border="0"/>

求解变成：

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{p^{k+1}=\arg \min _{p \in Y} \mathcal{D}^{*}(p, g)-\left\langle A_{S} \tilde{u}^{k}, p\right\rangle+\frac{1}{2 \tau}\left\|p-p^{k}\right\|_{Y}^{2}} \\ {\tilde{u}^{k+1}=\arg \min _{\tilde{u} \in Y} \underset{\mathcal{R}}{\mathcal{R}}(\tilde{u})+\left\langle A_{S} \tilde{u}, p^{k+1}\right\rangle+\frac{1}{2 r}\left\|\tilde{u}-u^{k}\right\|_{X}^{2}} \\ {f^{k+1}=\arg \min _{f \in Z} \quad \mathcal{F}\left(A_{F} u^{k}, f\right)+\frac{1}{2 \sigma}\left\|f-f^{k}\right\|_{Z}^{2}} \\ {u^{k+1}=\arg \min _{u \in X} \mathcal{F}\left(A_{F} u, f\right)+\frac{1}{2 r}\left\|u-\tilde{u}^{k+1}\right\|_{X}^{2}}\end{array}\right." border="0"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200219170256.png"/>

### Bibtex:

```latex
@inproceedings{cheng2019learned,
  title={Learned Full-Sampling Reconstruction},
  author={Cheng, Weilin and Wang, Yu and Chi, Ying and Xie, Xuansong and Duan, Yuping},
  booktitle={International Conference on Medical Image Computing and Computer-Assisted Intervention},
  pages={375--384},
  year={2019},
  organization={Springer}
}
```

