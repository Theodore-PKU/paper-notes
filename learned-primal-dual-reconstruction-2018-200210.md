### Paper:

Learned Primal-dual Reconstruction

### Author:

Jonas Adler, Ozan Öktem

### Year:

2018

### Notes:

阅读日期：2020.02.10

泛读。这篇文章是基于 primal-dual 算法设计的 unroll 形式的网络。主要的想法就是将 PD 算法中的 proximal 算子变成一个可学习的模块（这样就变成 data-driven）。这篇文章引用率挺高的，个人认为，本质上和其他的 unroll 方法差不多。用三层的卷积网络作为学习的模块。激活函数是 PReLU。

**推导过程**

目标函数：注意，作者这里写的比较宽泛。

<img src="http://latex.codecogs.com/svg.latex? \min _{f \in X}[\mathcal{F}(\mathcal{K}(f))+\mathcal{G}(f)]" border="0"/>

求解算法：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200210205939.png" width="60%"/>

作者要做的就是把上述算法变成一个网络的形式，用可学习的算子来替代 proximal 算子。作者现有一个初步的改进：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200210210200.png" width="60%"/>

然后，作者进一步放宽各种限制，比如参数不共享，把 step 5 合并，不限制 step 3 和 step 4 的输入形式。得到如下的结果。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200210210305.png" width="60%"/>

网络结构（其实就是三层卷积网络，不过有一个 residual 的形式，这和 PD 算法本身倒是很相近）：

<img src="http://latex.codecogs.com/svg.latex? \mathrm{Id}+\mathcal{W}_{w_{3}, b_{3}} \circ \mathcal{A}_{c_{2}} \circ \mathcal{W}_{w_{2}, b_{2}} \circ \mathcal{A}_{c_{1}} \circ \mathcal{W}_{w_{1}, b_{1}}" border="0"/>

PReLU 的形式是：
$$
\mathcal{A}_{c_{j}}(x)=\left\{\begin{array}{ll}{x} & {\text { if } x \geq 0} \\ {-c_{j} x} & {\text { else }}\end{array}\right.
$$

### Bibtex:

```latex
@article{adler2018learned,
  title={Learned primal-dual reconstruction},
  author={Adler, Jonas and {\"O}ktem, Ozan},
  journal={IEEE transactions on medical imaging},
  volume={37},
  number={6},
  pages={1322--1332},
  year={2018},
  publisher={IEEE}
}
```

