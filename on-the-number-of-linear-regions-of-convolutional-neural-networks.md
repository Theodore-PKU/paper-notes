### On the Number of Linear Regions of Convolutional Neural Networks

Author: Huan Xiong, Lei Huang, Mengyang Yu, Li Liu, Fan Zhu, Ling Shao

Year: 2020

Published in: ICML 2020

```latex
@article{xiong2020number,
  title={On the Number of Linear Regions of Convolutional Neural Networks},
  author={Xiong, Huan and Huang, Lei and Yu, Mengyang and Liu, Li and Zhu, Fan and Shao, Ling},
  journal={arXiv preprint arXiv:2006.00978},
  year={2020}
}
```

[Link](https://github.com/Theodore-PKU/paper-notes/blob/master/on-the-number-of-linear-regions-of-convolutional-neural-networks.md)

Note: /Users/xieyutong/Documents/Research/PaperReading/Notes/on-the-number-of-linear-regions-of-convolutional-neural-networks.md



**Motivation**

关于线性区域的数量，大多数关心的是全连接网络，作者表示没有见到卷积网络的计算。



**主要贡献**

1. 给出单层 ReLU CNN 网络的线性区域数量的最大值和平均值。
2. 推导了一个多层 ReLU CNN 网络的线性区域数量的上界和下界。



**前期准备**

卷积层：不考虑池化层和全连接层，也不考虑 zero-padding，只有卷积层和 ReLU 层。一共 $L$ 个隐层。

记号：$n_0^{(1)}\times n_0^{(2)}\times d_0$ 是输入，$n_0^{(l)}\times n_l^{(2)}\times d_l$ 是第 $l$ 层的输出，卷积的维数是 $f_l^{(1)}\times f_l^{(2)}\times d_{l-1}$，stride 为 $s_l$.

activation pattern：任何输入，可以计算每个结点 ReLU 函数是否被激活，整个网络的激活情况称为 activation pattern.

线性区域：相同 activation pattern 的输入的集合。

**Lemma 2.** *Let $\mathcal{N}$ be a ReLU CNN with $n$ hidden neurons. Then, the number $R_\mathcal{N}$ of linear regions of  $\mathcal{N}$ is at most $2^ n$ .*

**Theorem 1** (Proposition 2 from Pascanu et al., 2013). *Let $\mathcal{N}$ be a one-layer ReLU NN with $n_0$ input neurons and $n_1$ hidden neurons. Then, the maximal number of linear regions of $\mathcal{N}$ is equal to $\sum_{i=0}^{n_{0}}\left(\begin{array}{c}
n_{1} \\
i
\end{array}\right)$ .*



**主要结论**

**Theorem 2.** *Suppose that the parameters $θ = \{ W, B \}$ are drawn from a ﬁxed distribution $\mu$ which has densities with respect to Lebesgue measure in $\mathbb{R}^{\text{#weights} + \text{#bias}}$. Define $I_{\mathcal{N}}=\{(i, j): \quad 1 \leq i \leq \left.n_{1}^{(1)}, 1 \leq j \leq n_{1}^{(2)}\right\}$ and $S_{\mathcal{N}}=\left(S_{i, j}\right)_{n_{1}^{(1)} \times n_{1}^{(2)}}$ where*
$$
S_{i, j}=\left\{\left(a+(i-1) s_{1}, b+(j-1) s_{1}, c\right): 1 \leq a \leq f_{1}^{(1)}\right. \left.1 \leq b \leq f_{1}^{(2)}, 1 \leq c \leq d_{0}\right\}
$$
*Let*
$$
K_{\mathcal{N}}:=\left\{\left(t_{i, j}\right)_{(i, j) \in I_{\mathcal{N}}}: t_{i, j} \in \mathbb{N}\right.
\left.\sum_{(i, j) \in J} t_{i, j} \leq \# \cup_{(i, j) \in J} S_{i, j} \forall J \subseteq I_{\mathcal{N}}\right\}
$$
*Then*
$$
R_{\mathcal{N}}=\sum_{\left(t_{i, j}\right)_{(i, j) \in I_{\mathcal{N}}} \in K_{\mathcal{N}}} \prod_{(i, j) \in I_{\mathcal{N}}}\left(\begin{array}{l}d_{1} \\ t_{i, j}\end{array}\right)
$$

$$
\mathbb{E}_{\theta \sim \mu}\left[R_{\mathcal{N}, \theta}\right]=\sum_{\left(t_{i, j}\right)_{(i, j) \in I_{\mathcal{N}}} \in K_{\mathcal{N}}} \prod_{(i, j) \in I_{\mathcal{N}}}\left(\begin{array}{l}d_{1} \\ t_{i, j}\end{array}\right)
$$

证明思路：

1. **Lemma 6** (Theorems 2.4 and 2.5 from Stanley, 2004). *Let $\mathcal{A}$ be an arrangement in an $n$-dimensional vector space. Then we have*

$$
r(\mathcal{A})=\sum_{\mathcal{B} \subseteq \mathcal{A} \atop \mathcal{B} \text { central}}(-1)^{\# \mathcal{B}-\operatorname{rank}(\mathcal{B})}
$$

- $\# \mathcal{B}$ is the number of hyperplanes.
- $\operatorname{rank}(\mathcal{B})$ is the dimension of the space spanned by the normal vectors of the hyperplanes in $\mathcal{B}$.
- An arrangement $\mathcal{B}$ is called central if $\cap_{H \in \mathcal{B}} H \neq \empty$.

2. 

![屏幕快照 2020-06-12 上午9.18.18](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202020-06-12%20%E4%B8%8A%E5%8D%889.18.18.png)

3. 

![屏幕快照 2020-06-12 上午9.34.49](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-06-12 上午9.34.49.png)

Lemma 6 可以推出 Proposition 2，Proposition 2 对应 Theorem 2 的上界，Lemma 11 证明 Proposition 2 中的两个条件几乎处处成立，所以等号成立（(3)式），并且在任何勒贝格测度下，期望也取等号（(4)式）.



**Theorem 5.** *The maximal number $R_{\mathcal{N}}$ of linear regions of $\mathcal{N}$* with

*Lower bound:* 
$$
R_{\mathcal{N}} \geq R_{\mathcal{N}^{\prime}} \prod_{l=1}^{L-1}\left\lfloor\frac{d_{l}}{d_{0}}\right\rfloor^{n_{l}^{(1)} \times n_{l}^{(2)} \times d_{0}}
$$
*where $\mathcal{N}^{\prime}$ is a one-layer ReLU CNN which has input dimension $n_{L−1}^{(1)} \times n_{L−1}^{(2)} \times d_0$ (the third dimension is $d_0$ , not $d_{L−1}$ ), hidden layer dimension $n_{L}^{(1)} \times n_{L}^{(2)} \times d_L$, and $d_L$  ﬁlters with dimension $f_L^{(1)} \times f_L^{(2)}× d_0$ and stride $s_L$.*

*Upper bound:* 
$$
R_{\mathcal{N}} \leq R_{\mathcal{N}^{\prime \prime}} \prod_{l=2}^{L} \sum_{i=0}^{n_{0}^{(1)} n_{0}^{(2)} d_{0}}\left(\begin{array}{c}n_{l}^{(1)} n_{l}^{(2)} d_{l} \\ i\end{array}\right)
$$
*where $\mathcal{N}^{\prime\prime}$ is a one-layer ReLU CNN which has input dimension $n_{0}^{(1)} \times n_{0}^{(2)} \times d_{0}$, hidden layer dimension $n_{1}^{(1)} \times n_{1}^{(2)} \times d_{1}$ and $d_1$ ﬁlters with dimension $f_0^{(1)} \times f_0^{(2)}× d_0$ and stride $s_1$.*

证明思路：

Lower bound: 将卷积层分解，看成两个卷积的复合。那么可以构造出第一个卷积层将输入先映射到一个 hypercube，然后在 hypercube 上进行区域划分。

Upper bound: 把除了第一层以外的其他层当作是全连接层进行推导。



**实验**

主要内容是计算论文中给出的例子的理论线性区域数量和实际通过采样方法得到的数量。

采样方法：用高斯分布在输入空间进行采样，统计 activation pattern 的数量。

![屏幕快照 2020-06-12 上午8.42.00](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-06-12 上午8.42.00.png)



**反思与总结**

1. Theorem 2 的证明中，上界可以取到并且是几乎处处取到的合理来源是 Lemma 11，但是没有考虑到卷积层的共享参数，所以证明上可能有瑕疵。
2. 多层 ReLU CNN 的下界证明思路和以往文献中 MLP 中的下界构造方法一样。
3. 多层 ReLU CNN 的上界证明非常粗糙，实际上只有第一层应用了单层卷积的特点，后面层的推导和传统 MLP 的构造也是一样的。但是这也是因为 Theorem 2 只提供了划分数量，不能提供第二次划分的新的信息，比如 space dimension（相当于 Lemma 6 中的 $\text{rank}$ 的定义）.

Theorem 2 给出的结论和证明方法很有启发性，特别是 hyperplane arrangement 的应用（这也意味着 hyperplane arrangement 的其他研究对这个问题的进一步研究可能有帮助）。上下界的证明没有新意。。。

如何把 Theorem 2 拓展到 space dimension 的划分结果是之后需要研究的内容。