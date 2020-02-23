### Paper:

Accelerating MR Imaging via Deep Chambolle-Pock Network

### Author:

Haifeng Wang, Jing Cheng, Sen Jia, Zhilang Qiu, Caiyun Shi, Lixian Zou, Shi Su, Yuchou Chang, Yanjie Zhu, Leslie Ying, and Dong Liang

### Year:

2019

### Notes:

阅读时间：2020.01.25

泛读。这篇文章研究的是MRI重建。作者的思路是 unroll network，展开的算法是 Chambolle-Pock 算法（primal-dual first-order algorithm）。关于参数化部分，作者只给出了结果，没有给出分析和说明。

设目标函数为：

<img src="http://latex.codecogs.com/svg.latex? \min F(A p)+G(p)" border="0"/>

其中，$F$ 表示 data fidelity，$G$ 表示 regularized term.

 Chambolle-Pock algorithm 的迭代形式为：

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{d_{n+1}=\operatorname{prox}_{\sigma}\left[F^{*}\right]\left(d_{n}+\sigma A \bar{p}_{n}\right)} \\ {p_{n+1}=\operatorname{prox}_{\tau}[G]\left(p_{n}-\tau A^{*} d_{n+1}\right)} \\ {\bar{p}_{n+1}=p_{n+1}+\theta\left(p_{n+1}-p_{n}\right)}\end{array}\right." border="0"/>

作者把 proximal 算子参数化为卷积网络：

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{d_{n+1}=\Gamma\left(d_{n}+\sigma A \bar{p}_{n}, y\right)} \\ {p_{n+1}=\Lambda\left(p_{n}-\tau A^{*} d_{n+1}\right)} \\ {\bar{p}_{n+1}=p_{n+1}+\theta\left(p_{n+1}-p_{n}\right)}\end{array}\right." border="0"/>

其中 $\Gamma, \Lambda$ 表示的都是神经网络，其结构分别是 4-32-32-2, 2-32-32-2 结构的 res block。作者表示效果比 admm-net 和 cascade net 好。网络结构示意图。（作者没有明确说是否共享参数）

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-25%E4%B8%8B%E5%8D%883.52.2.png"/>

### Bibtex:

```latex
@inproceedings{wang2019accelerating,
  title={Accelerating MR imaging via deep Chambolle-Pock network},
  author={Wang, Haifeng and Cheng, Jing and Jia, Sen and Qiu, Zhilang and Shi, Caiyun and Zou, Lixian and Su, Shi and Chang, Yuchou and Zhu, Yanjie and Ying, Leslie and others},
  booktitle={2019 41st Annual International Conference of the IEEE Engineering in Medicine and Biology Society (EMBC)},
  pages={6818--6821},
  year={2019},
  organization={IEEE}
}
```

