### Error bounds for approximations with deep ReLU networks

Author: Dmitry Yarotsky

Year: 2017

Published in: Neural Networks

```latex
@article{yarotsky2017error,
  title={Error bounds for approximations with deep ReLU networks},
  author={Yarotsky, Dmitry},
  journal={Neural Networks},
  volume={94},
  pages={103--114},
  year={2017},
  publisher={Elsevier}
}
```

https://github.com/Theodore-PKU/paper-notes/blob/master/error-bounds-for-approximations-with-deep-relu-networks.md

/Users/xieyutong/Documents/Research/PaperReading/Notes/error-bounds-for-approximations-with-deep-relu-networks.md



这篇文章的结论分为两个部分，一个是 ReLU 网络逼近函数时的上界，即存在 ReLU 网络，给定逼近精度时，其参数量、网络深度有限并且可以实现这一精度的逼近；另一个是下界，即 ReLU 网络在给定逼近精度时，至少需要多少结点或计算量，否则无法实现该精度的逼近。

首先作者讨论了不同分片线性激活函数和 ReLU 激活函数的不同网络之间的相互转换关系。

上界部分的证明思路

先证明 $x^2, xy$ 可以被逼近，具体为：

1. 对任意精度 $\epsilon$，存在 ReLU 网络，深度和节点数是 $O(\ln (1/\epsilon))$，可以逼近 $[0,1]$ 上的 $f(x) = x^2$

   逼近方式是通过函数 $g$ 的复合：
   $$
   g(x)=\left\{\begin{array}{ll}
   2 x, & x<\frac{1}{2} \\
   2(1-x), & x \geq \frac{1}{2}
   \end{array}\right.
   $$

   $$
   g_{s}(x)=\underbrace{g \circ g \circ \cdots \circ g}_{s}(x)\\
   f_{m}\left(\frac{k}{2^{m}}\right)=\left(\frac{k}{2^{m}}\right)^{2}, \quad k=0, \ldots, 2^{m}\\
   f_{m-1}(x)-f_{m}(x)=\frac{g_{m}(x)}{2^{2 m}}
   $$

   $f_m$ 是对 $f$ 的逼近，而 $f_{m}(x)=x-\sum_{s=1}^{m} \frac{g_{s}(x)}{2^{2 s}}$，因此，可以设计如下的网络结构：

   <img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/image-20200417192909251.png" alt="image-20200417192909251" style="zoom:50%;" />

2. 利用 $x y=\frac{1}{2}\left((x+y)^{2}-x^{2}-y^{2}\right)$，可以构建出在 $[0, M]\times[0,M]$ 上满足 $\epsilon$ 精度的 $xy$ 逼近网络，其深度和计算节点不多于 $c_{1} \ln (1 / \epsilon)+c_{2}, c_2 = c_2(M)$

然后证明对任意函数的逼近方式。对任意函数 $f$ 用泰勒展开的形式逼近，具体写为：
$$
f_{1}=\sum_{\mathbf{m} \in\{0, \ldots, N\}^{d}} \phi_{\mathbf{m}} P_{\mathbf{m}}
$$
$\boldsymbol{m}$ 表示格点，$\phi_m$ 类似于冲击函数。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/image-20200417193615072.png" alt="image-20200417193615072" style="zoom:50%;" />
$$
P_{\mathbf{m}}(\mathbf{x})=\left.\sum_{\mathbf{n}:|\mathbf{n}|<n} \frac{D^{\mathbf{n}} f}{\mathbf{n} !}\right|_{\mathbf{x}=\frac{\mathbf{m}}{N}}\left(\mathbf{x}-\frac{\mathbf{m}}{N}\right)^{\mathbf{n}}
$$
很显然，$P_m(x)$ 是局部泰勒展开。则 $f_1$ 可以写为：
$$
f_{1}(\mathbf{x})=\sum_{\mathbf{m} \in\{0, \ldots, N\}^{d}} \sum_{\mathbf{n}: \mathbf{n} |<n} a_{\mathbf{m}, \mathbf{n}} \phi_{\mathbf{m}}(\mathbf{x})\left(\mathbf{x}-\frac{\mathbf{m}}{N}\right)^{\mathbf{n}}
$$
每一项 $\phi_{\mathbf{m}}(\mathbf{x})\left(\mathbf{x}-\frac{\mathbf{m}}{N}\right)^{\mathbf{n}}$ 都可以利用 $xy$ 的逼近设计 ReLU 网络。最终的逼近网络的深度最多为 $c(\ln (1 / \epsilon)+1)$，计算结点数为 $c \epsilon^{-d / n}(\ln (1 / \epsilon)+1)$，$c = c(d,n)$。$d$ 指的是 $f$ 的自变量的维数，$n$ 是和导数性质有关的量。$d, n$ 表示了一类函数：
$$
F_{n, d}=\left\{f \in \mathcal{W}^{n, \infty}\left([0,1]^{d}\right):\|f\|_{\mathcal{W}^{n, \infty}\left([0,1]^{d}\right)} \leq 1\right\}
$$


下界部分的结论则是给定不同条件下，至少需要多深、多少结点的 ReLU 网络的假设才能逼近目标函数。

