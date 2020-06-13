### Approximating Continuous Function by ReLU Nets of Minimal Width

Author: Boris Hanin and Mark Sellke

Year: 2017

arXiv preprint

```latex
@article{hanin2017approximating,
  title={Approximating continuous functions by relu nets of minimal width},
  author={Hanin, Boris and Sellke, Mark},
  journal={arXiv preprint arXiv:1710.11278},
  year={2017}
}
```

https://github.com/Theodore-PKU/paper-notes/blob/master/approximating-continuous-function-by-relu-nets-of-minimal-width.md

/Users/xieyutong/Documents/Research/PaperReading/Notes/approximating-continuous-function-by-relu-nets-of-minimal-width.md

这篇文章考虑的是 $w(d_{in}, d_{out})$，它的定义是当  ReLU 网络在逼近任意连续函数 $f:[0,1]^{d_{in}}\rightarrow \mathbb{R}^{d_{out}}$时，对任意的逼近精度，总是存在一个网络，宽度满足最宽处为 $w$，$w(d_{in},d_{out})$ 就是对于该函数，满足该条件的 $w$ 的最小值。

整篇文章是关于 $w(d_{in}, d_{out})$ 的上下界。结论是：
$$
d_{i n}+1 \leq w_{\min }\left(d_{i n}, d_{o u t}\right) \leq d_{i n}+d_{o u t}
$$
换句话说，当网络宽度为 $d_{in} + d_{out}$ 时，对任意逼近精度，都存在一个网络满足逼近条件，但是网络深度取决于函数本身和逼近精度；当网络宽度 $\leq d_{in}$ 时，总是存在某一连续函数，使得该网络和该函数之间的逼近程度有限 （无法无限逼近）。

证明思路：分成上界和下界两个部分。

上界部分：可以构造一个网络，宽度为 $d_{in}+d_{out}$ 时，可以实现以任意精度逼近任何一个函数。

下界部分：构造一个函数，使得任何一个宽度为 $d_{in}$ 的网络，都无法毕竟逼近该函数。

上界部分的证明中，作者先证明如下的函数可以等价于一个 ReLU 网络（$\ell$ 是线性变换）
$$
g=\sigma_{L-1}\left(\ell_{L}, \sigma_{L-2}\left(\ell_{L-1}, \ldots, \sigma_{2}\left(\ell_{3}, \sigma_{1}\left(\ell_{1}, \ell_{2}\right)\right) \cdots\right)\right.
$$
等价的 ReLU 网络具有比较特别的结构：
$$
A_{j}(x, y)=\left\{\begin{array}{ll}
\left(x, y-\ell_{j}(x)\right), & \text { if } \sigma_{j-1}=\max \\
\left(x,-y+\ell_{j}(x)\right), & \text { if } \sigma_{j-1}=\min
\end{array}\right.
\\
A_{j}^{-1}(x, y)=\left\{\begin{array}{ll}
\left(x, y+\ell_{j}(x)\right), & \text { if } \sigma_{j-1}=\max \\
\left(x,-y+\ell_{j}(x)\right), & \text { if } \sigma_{j-1}=\min
\end{array}\right.\\
H_{j}:=A_{j}^{-1} \circ \operatorname{ReLU} \circ A_{j}\\
$$
网络结构可以表示为：
$$
\mathrm{ReLU} \circ H_{L} \circ \cdots \circ H_{1}, H_1 = A_1 = (x, l_1(x))
$$
然后证明 $g$ 可以任意逼近任意函数 $f:\mathbb{R}^{d_{in}}\rightarrow \mathbb{R}^{d_{out}}$. $g$ 的证明则是通过从较小的定义域（半径很小的球）出发，不断增加定义域的范围（球面处增加一小部分，实际是一个三角区域，多次累加可以覆盖整个球面，使得定义域球的半径变大），同时增加 $g$ 的层数，使得在新的定义域上可以逼近函数 $f$。$g$ 增加的内容形如
$$
\widehat{g}=\max \left(\ell_{-}, \min \left(\ell_{+}, g\right)\right)
$$
 而 $\ell_{-}, \ell_+$ 这两个线性变换和增加的区域是直接相关的（和三角区域的顶点相关）。换言之，逼近函数 $g$ 的构造和区域划分是直接相关的，这种构造方法可以理解为某种形式的区域划分，然后逐个区域给出对应的函数值。

下界部分的证明中，作者先给出宽度为 $d_{in}$ 时表示的函数的一个性质，然后利用这个性质用反证法证明存在一个函数，其水平集函数是无法逼近的。



