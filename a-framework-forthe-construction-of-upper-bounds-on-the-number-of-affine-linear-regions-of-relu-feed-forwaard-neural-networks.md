## A Framework for the construction of upper bounds on the number of aﬃne linear regions of ReLU feed-forward neural networks 详细笔记

Author: Peter Hinz, Sara van de Geer

Year: 2019

Published in: IEEE Transactions on Information Theory

```latex
@article{hinz2019framework,
  title={A framework for the construction of upper bounds on the number of affine linear regions of ReLU feed-forward neural networks},
  author={Hinz, Peter and Van de Geer, Sara},
  journal={IEEE Transactions on Information Theory},
  volume={65},
  number={11},
  pages={7304--7324},
  year={2019},
  publisher={IEEE}
}
```

Link: https://github.com/Theodore-PKU/paper-notes/blob/master/a-framework-forthe-construction-of-upper-bounds-on-the-number-of-affine-linear-regions-of-relu-feed-forwaard-neural-networks.md

Note: /Users/xieyutong/Documents/Research/PaperReading/Notes/a-framework-forthe-construction-of-upper-bounds-on-the-number-of-affine-linear-regions-of-relu-feed-forwaard-neural-networks.md



### 记号

$h$：$\mathbb{R}^{n} \rightarrow \mathbb{R}^{n^{\prime}}, x \mapsto\left(\sigma\left(\left\langle x, w_{i}^{(h)}\right\rangle+b_{i}^{(h)}\right)\right)_{i \in\left\{1, \ldots, n^{\prime}\right\}}$，线性层 + ReLU 层

$RL(n,n'):=\left\{h: \mathbb{R}^{n} \rightarrow \mathbb{R}^{n^{\prime}} | h \text { is a ReLU Layer function }\right\}$，$h$ 的集合，也就是单层网络

$H^{h}_i:=\left\{x \in \mathbb{R}^{n} |\left\langle x, w_{i}^{(h)}\right\rangle+b_{i}^{(h)}=0\right\} \subset \mathbb{R}^{n} \quad \text { for } i \in\left\{1, \ldots, n^{\prime}\right\}$，$h$ 的第 $i$ 个超平面

$\mathrm{RL}\left(n_{0}, \mathbf{n}\right):=\operatorname{RL}\left(n_{0}, n_{1}\right) \times \operatorname{RL}\left(n_{1}, n_{2}\right) \times \cdots \times \operatorname{RL}\left(n_{L-1}, n_{L}\right), \mathbf{n}=\left(n_{1}, \ldots, n_{L}\right) \in \mathbb{N}_{+}^{L}$，$L$ 层网络的各层函数集合，也可以表示为 $\mathbf{h}=\left(h_{1}, \ldots, h_{L}\right) \in \mathrm{RL}\left(n_{0}, \mathbf{n}\right)$，网络记为：
$$
f_{\mathbf{h}}:\left\{\begin{array}{ll}
\mathbb{R}^{n_{0}} & \rightarrow \mathbb{R}^{n_{L}} \\
x & \mapsto h_{L} \circ \cdots \circ h_{1}(x)
\end{array}\right.
$$


### 定义

$S_{h}(x) \in\{0,1\}^{n^{\prime}}$ 表示输入 $x$ 在 $h$ 运算中的激活 pattern

$R_{h}(s):=\left\{x \in \mathbb{R}^{n} | S_{h}(x)=s\right\}$：单层网络某一激活 pattern 对应的输入空间的区域

$\mathcal{S}_{h}:=\left\{S_{h}(x) \in\{0,1\}^{n^{\prime}} | x \in \mathbb{R}^{n}\right\}$：$h$ 运算下所有可能的激活 pattern

$S_{\mathbf{h}}(x)=\left(S_{h_{1}}(x), S_{h_{2}}\left(h_{1}(x)\right), \ldots, S_{h_{L}}\left(h_{L-1} \circ \cdots \circ h_{1}(x)\right)\right)$：多层网络的激活 pattern

$R_{\mathbf{h}}(s):=\left\{x \in \mathbb{R}^{n_{0}} | S_{\mathbf{h}}(x)=s\right\}$：多层网络的某一激活 pattern 对应的输入空间的区域。

$\mathcal{S}_{\mathbf{h}}:=\left\{S_{\mathbf{h}}(x) \in\{0,1\}^{n_{1}} \times \cdots \times\{0,1\}^{n_{L}} | x \in \mathbb{R}^{n_{0}}\right\}$：多层网络的所有可能的激活 pattern

我们所关心的就是 $|\mathcal{S}_{\mathbf{h}}|$ 的大小



$\mathcal{P}_{f}=\{P \in \mathcal{P} | \forall R \in P, f \text { is affine linear on } R\}$：定义函数 $f$  对应的一个可能的划分，$\mathcal{P}$ 是输入空间所有可能的划分。严格的区域划分的数量可以定义为：
$$
N_{f}=\inf _{P \in \mathcal{P}_{f}}|P|
$$
有了区域划分之后，函数 $f$ 就很容易看成分片线性函数。

并且有激活 pattern 数量和区域划分数量的关系：
$$
N_{f_{\mathrm{h}}} \leq\left|\mathcal{S}_{\mathbf{h}}\right|
$$


### 三个经典上界

$$
N_{f_{h}} \leq 2^{\sum_{l=1}^{L} n_{l}}
$$


$$
N_{f_{\mathrm{h}}} \leq \prod_{l=1}^{L} \sum_{j=0}^{\min \left(n_{0}, \ldots, n_{l-1}\right)}\left(\begin{array}{c}
n_{l} \\
j
\end{array}\right)
$$

$$
N_{f_{\mathrm{h}}} \leq \sum_{j_{1}, \ldots, j_{L} \in J} \prod_{l=1}^{L}\left(\begin{array}{c}
n_{l} \\
j_i
\end{array}\right)\\
J=\left\{\left(j_{1}, \ldots, j_{L}\right) \in \mathbb{N}^{L} | \forall l \in\{1, \ldots, L\}: j_{l} \leq \min \left(n_{0}, n_{1}-j_{1}, \ldots, n_{L-1}-j_{L-1}, n_{L}\right)\right\}
$$



### 新的计算框架

定义激活 pattern 序列：
$$
\begin{aligned}
&\mathcal{S}_{\mathbf{h}}^{(1)}:=\left\{\left(s_{1}\right) \in\{0,1\}^{n_{1}} |\left(s_{1}, \ldots, s_{L}\right):=s \in \mathcal{S}_{\mathrm{h}}\right\}\\
&\mathcal{S}_{\mathbf{h}}^{(2)}:=\left\{\left(s_{1}, s_{2}\right) \in\{0,1\}^{n_{1}} \times\{0,1\}^{n_{2}} |\left(s_{1}, \ldots, s_{L}\right):=s \in \mathcal{S}_{\mathbf{h}}\right\}\\
&\vdots\\
&\mathcal{S}_{\mathbf{h}}^{(L)}:=\quad\left\{\left(s_{1}, \ldots, s_{L}\right) \in\{0,1\}^{n_{1}} \times \cdots \times\{0,1\}^{n_{L}} |\left(s_{1}, \ldots, s_{L}\right):=s \in \mathcal{S}_{\mathrm{h}}\right\}=\mathcal{S}_{\mathrm{h}}
\end{aligned}
$$
核心计算：
$$
\left|\mathcal{S}_{\mathbf{h}}^{(1)}\right| \leq \sum_{j=0}^{n_{0}}\left(\begin{array}{c}
n_{1} \\
j
\end{array}\right)\\
\left|\mathcal{S}_{\mathbf{h}}^{(l+1)}\right| \leq \sum_{\left(s_{1}, \dots, s_{l}\right) \in \mathcal{S}_{\mathbf{h}}^{(l)}} \sum_{j=0}^{\min \left(n_{0},\left|s_{1}\right|, \dots,\left|s_{l}\right|\right)}\left(\begin{array}{c}
n_{l+1} \\
j
\end{array}\right)
$$
定义 histgram：$\tilde{\mathcal{H}}^{(l)}\left(\mathcal{S}_{\mathrm{h}}^{(l)}\right)$ 为集合 $ \left\{\min \left(n_{0},\left|s_{1}\right|, \ldots,\left|s_{l}\right|\right)| s \in \mathcal{S}_{\mathbf{h}}^{(l)}\right\}$ 的 histgram，表示当前所有激活 pattern 的最小值 $\min\{n_0, |s_i|\}$ 的分布。

通过定一个一个比较运算 $\preceq$，可以得到如下的关系：
$$
\begin{aligned}
\tilde{\mathcal{H}}^{(1)}\left(\mathcal{S}_{\mathbf{h}}^{(1)}\right) & \preceq \varphi_{n_{1}}^{(\gamma)}\left(\mathbf{e}_{n_{0}}\right) \\
\tilde{\mathcal{H}}^{(l+1)}\left(\mathcal{S}_{\mathbf{h}}^{(l+1)}\right) & \preceq \varphi_{n_{l+1}}^{(\gamma)}\left(\tilde{\mathcal{H}}^{(l)}\left(\mathcal{S}_{\mathbf{h}}^{(l)}\right)\right) \quad \text { for } l \in\{1, \ldots, L-1\}
\end{aligned}
$$
$\varphi_{n_{i}}^{(\gamma)}$ 表示输入的 histgram 和输出 histgram 之间的转换关系。

这个比较运算刻画了一个性质：总是希望划分以后的空间的维度尽可能大，这样进一步划分的数量会更多。

These transition functions have a monotonicity property such that smaller inputs yield smaller outputs.

则 $\tilde{\mathcal{H}}^{(L)} \left(\mathcal{S}_{\mathbf{h}}^{(L)}\right) \preceq \varphi_{n_{L}}^{(\gamma)} \circ \cdots \circ \varphi_{n_{1}}^{(\gamma)}\left(e_{n_{0}}\right)$



严格的数学表示

$V=\left\{x \in \mathbb{N}^{\mathbb{N}} |\|x\|_{1}=\sum_{j=0}^{\infty} x_{j}<\infty\right\}$ 是 histgram 的集合

比较关系 $\preceq$ 定义为：$v \preceq w \quad: \Longleftrightarrow \forall J \in \mathbb{N}: \sum_{j=J}^{\infty} v_{j} \leq \sum_{j=J}^{\infty} w_{j}$

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/image-20200427113126073.png" alt="image-20200427113126073" style="zoom:50%;" />

并不是所有的 histgram 都可以比较，但是对于任意两个 histgram，存在第三个 histgram 总是大于它们。

对于一个 histgram 的集合，定义最大的 histgram：
$$
\max _{i \in I}\left(v^{(i)}\right)_{J}=\max _{i \in I}\left(\sum_{j=J}^{\infty} v_{j}^{(i)}\right)-\max _{i \in I}\left(\sum_{j=J+1}^{\infty} v_{j}^{(i)}\right) \quad \text { for } J \in \mathbb{N}
$$
可以证明下面的结论：
$$
\forall J \in \mathbb{N}: \max _{i \in I}\left(\sum_{j=J}^{\infty} v_{j}^{(i)}\right)=\sum_{j=J}^{\infty} \max _{i \in I}\left(v^{(i)}\right)_{j}
$$
<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/image-20200427113049745.png" alt="image-20200427113049745" style="zoom:50%;" />

这个结论对于区域划分而言，说明了上述定义的最大值操作是合理的。the maximum of some histograms in V is their smallest dominating histogram in V.
$$
\mathcal{H}_{n^{\prime}}:\left\{\begin{array}{ll}
\mathcal{P}\left(\{0,1\}^{n^{\prime}}\right) & \rightarrow V \\
\mathcal{S} & \mapsto\left(\sum_{s \in \mathcal{S}} \mathbb{1}_{\{j\}}(|s|)\right)_{j \in \mathbb{N}}
\end{array}\right.\\
\tilde{\mathcal{H}}^{(l)}:\left\{\begin{array}{ll}
\mathcal{P}\left(\{0,1\}^{n_{1}} \times \cdots \times\{0,1\}^{n_{l}}\right) & \rightarrow V \\
U & \mapsto\left(\sum_{\left(s_{1}, \dots, s_{l}\right) \in U} \mathbb{1}_{\{j\}}\left(\min \left(n_{0},\left|s_{1}\right|, \ldots,\left|s_{l}\right|\right)\right)\right)_{j \in \mathbb{N}_{0}}
\end{array}\right.
$$
这两个定义则分别表示单层和多层网络的一个激活 pattern 集合对应的 histgram 的计算。（可以举例子）

$\min \left(n_{0},\left|s_{1}\right|, \ldots,\left|s_{l}\right|\right)$ 表示激活 pattern 为 $s$ 的区域的 aﬃne linear map 的秩，也就是 image 的维数的上界



核心的问题是 $\varphi^{\gamma}$ 的计算，其中 $\gamma$ 是关键。下面的定义给出了 $\gamma$ 的 具体内容：

1. $\forall n^{\prime} \in \mathbb{N}_{+}, n \in\left\{0, \ldots, n^{\prime}\right\} \quad \max \left\{\mathcal{H}_{n^{\prime}}\left(\mathcal{S}_{h}\right) | h \in \operatorname{RL}\left(n, n^{\prime}\right)\right\} \preceq \gamma_{n, n^{\prime}}$
2. $\forall n^{\prime} \in \mathbb{N}_{+}, n, \tilde{n} \in\left\{0, \ldots, n^{\prime}\right\} \quad n \leq \tilde{n} \Longrightarrow \gamma_{n, n^{\prime}} \preceq \gamma_{\tilde{n}, n^{\prime}}$

$n'\in \mathbb{N}^+,0\leq n\leq n'$

第一个条件的含义是 $\gamma_{n,n'}$ 应该大于任何一个 $n$ 维输入空间、$n'$ 个超平面划分结果的 histgram，也就是输出 image 的维数 histgram。

第二个条件的含义是当输入空间的维数变大时，相同数量的超平面划分的 histgram 总是更优的。

这两个条件刻画了递推式的本质含义。

为了方便，对于 $n = 0$ 的特殊定义：
$$
n^{\prime} \in \mathbb{N}_{+}, \operatorname{RL}\left(0, n^{\prime}\right)=\left\{h:\{0\} \rightarrow \mathbb{R}^{n^{\prime}}, x \mapsto c | c \in \mathbb{R}\right\}\\
h \in \mathrm{RL}\left(0, n^{\prime}\right), \mathcal{H}_{n^{\prime}}\left(\mathcal{S}_{h}\right)=\mathrm{e}_{0}
$$
满足条件的 $\gamma_{n,n'}$ 的集合记为 $\Gamma$。对于 $n > n'$ 的情形，$\gamma_{n,n'}$ 取 $n = n'$  的情形即可。

定义一个功能性函数 clipping function，作用在某一个 histgram 上：
$$
\mathrm{cl}_{i^{*}}(v)_{i}=\left\{\begin{array}{ll}v_{i} & \text { for } i<i^{*} \\ \sum_{j=i^{*}}^{\infty} v_{j} & \text { for } i=i^{*} \\ 0 & \text { for } i>i^{*}\end{array}\right.
$$
<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/image-20200427113000777.png" alt="image-20200427113000777" style="zoom:50%;" />

考虑到输出 image 的维数小于等于输入空间的维数，所以对于 $h \in RL(n,n')$
$$
\left(\sum_{s \in \mathcal{S}_{h}} \mathbb{1}_{\{i\}}\left(\operatorname{rank} \text { of } h \text { on } R_{h}(s)\right)\right) \quad \preceq \mathrm{cl}_{n}\left(\gamma_{n, n^{\prime}}\right)
$$
于是可以定义：
$$
\varphi_{n^{\prime}}^{(\gamma)}:\left\{\begin{array}{ll}
V & \rightarrow V \\
v & \mapsto \sum_{n=0}^{\infty} v_{n} \mathrm{cl}_{\min \left(n, n^{\prime}\right)}\left(\gamma_{\min \left(n, n^{\prime}\right), n^{\prime}}\right)
\end{array}\right.
$$
利用 $v_1 \preceq v_2$ 可以推出 $\left\|v_{1}\right\|_{1} \leq\left\|v_{2}\right\|_{1}$，我们所关心的区域划分的上界 $\left|\mathcal{S}_{\mathbf{h}}\right|$ 可以表示为
$$
\left|\mathcal{S}_{\mathbf{h}}\right|=\left\|\tilde{\mathcal{H}}^{(L)}\left(\mathcal{S}_{\mathbf{h}}^{(L)}\right)\right\|_{1} \leq\left\|\varphi_{n_{L}}^{(\gamma)} \circ \cdots \circ \varphi_{n_{1}}^{(\gamma)}\left(\mathrm{e}_{n_{0}}\right)\right\|_{1} \quad \text { for } \gamma \in \Gamma
$$
而这个形式恰好可以写成矩阵形式：
$$
\left(B_{n^{\prime}}^{(\gamma)}\right)_{i, j}=\left(\varphi_{n^{\prime}}^{(\gamma)}\left(\mathrm{e}_{j-1}\right)\right)_{i-1}=\left(\mathrm{cl}_{j-1}\left(\gamma_{j-1, n^{\prime}}\right)\right)_{i-1} \quad \text { for } i, j \in\left\{1, \ldots, n^{\prime}+1\right\}
$$
定义连接矩阵：
$$
M_{n, n^{\prime}} \in \mathbb{R}^{n^{\prime}+1 \times n+1}, \quad M_{i, j}=\delta_{i, \min \left(j, n^{\prime}+1\right)} \text { for } i \in\left\{1, \ldots, n^{\prime}+1\right\}, j \in\{1, \ldots, n+1\}
$$
例子：
$$
M_{4,2}=\left(\begin{array}{ccccc}
1 & 0 & 0 & 0 & 0 \\
0 & 1 & 0 & 0 & 0 \\
0 & 0 & 1 & 1 & 1
\end{array}\right), \quad M_{2,4}=\left(\begin{array}{ccc}
1 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 1 \\
0 & 0 & 0 \\
0 & 0 & 0
\end{array}\right)
$$
最终的计算为：
$$
\left|\mathcal{S}_{\mathbf{h}}\right| \leq\left\|B_{n_{L}}^{(\gamma)} M_{n_{L-1}, n_{L}} \ldots B_{n_{1}}^{(\gamma)} M_{n_{0}, n_{1}} e_{n_{0}+1}\right\|_{1}
$$

