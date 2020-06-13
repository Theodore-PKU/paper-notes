### Understanding Deep Neural Networks with Rectfied Linear Units

Author: Raman Arora, Amitabh Basu, Poorya Mianjy, Anirbit Mukherjee

Year: 2018

Published in: ICLR 2018

```
@article{arora2016understanding,
  title={Understanding deep neural networks with rectified linear units},
  author={Arora, Raman and Basu, Amitabh and Mianjy, Poorya and Mukherjee, Anirbit},
  journal={arXiv preprint arXiv:1611.01491},
  year={2016}
}
```

Link: https://github.com/Theodore-PKU/paper-notes/blob/master/understanding-deep-neural-networks-with-ectfied-linear-units.md

Note: /Users/xieyutong/Documents/Research/PaperReading/Notes/understanding-deep-neural-networks-with-ectfied-linear-units.md



这篇文章首先讨论了 ReLU DNN 和分片线性函数之间的关系，指出任何一个 ReLU DNN 都表示了分片线性函数，每个分片线性函数 $f:\mathbb{R}^{n} \rightarrow \mathbb{R}$ 都可以用深度最多为 $\left\lceil\log _{2}(n+1)\right\rceil+1$ 的 ReLU DNN 表示。

这部分的证明的要点是先把分片线性函数表示为：
$$
f=\sum_{j=1}^{p} s_{j}\left(\max _{i \in S_{j}} \ell_{i}\right)
$$
$\ell_i$ 都是仿射变换，也就是和每个分片上的线性函数相关。$s_j$ 取 1 或 -1。而 $\max$ 运算可以用 ReLU DNN 表示

<img src="/Users/xieyutong/Pictures/screenshot/image-20200417200846815.png" alt="image-20200417200846815" style="zoom:50%;" />

利用分片线性函数在函数空间中的稠密性（可以任意逼近一个连续函数），所以 ReLU DNN 显然可以任意逼近任意连续函数，且深度不超过 $\left\lceil\log _{2}(n+1)\right\rceil+1$

其次，关于深度，作者给出了如下的定理，存在一类函数，当它可以用 $k+1$ 层、宽度为 $w$ 的 ReLU DNN 表示时，若用 $k'+1$ 层 ReLU DNN 表示（$k' <= k$)），则后者的结点数至少为 $\frac{1}{2} k' w^{\frac{k}{k'}}$. 这个 定理一定程度上说明了深度的好处。

最后，作者提出了一个复杂度的概念，$\text{comp}_{\mathcal{H}}(n, k, w)$，定义为可以用深度为 $k+1$，宽度最多 $w$ 的网络（该网络属于定义好的 $\mathcal{H}$）表示的 $n$ 元函数的分片数量。作者表示这是一个整数规划的的问题。在前人的一些工作中，设计出一类网络 $\mathcal{H}$，可以达到 $\left(\left\lfloor\left(\frac{w}{n}\right)\right\rfloor\right)^{(k-1) n}\left(\sum_{j=0}^{n}\left(\begin{array}{c}w \\ j\end{array}\right)\right)$. 在这一部分，作者提出了一类函数，这些函数可以用设计好的网络来表示。这类网络的定义如下：
$$
\mathrm{ZONOTOPE}_{k, w, m}^{n}\left[\mathbf{a}^{1}, \ldots, \mathbf{a}^{k}, \mathbf{b}^{1}, \ldots, \mathbf{b}^{m}\right]:=H_{\mathbf{a}^{1}, \ldots, \mathbf{a}^{k}} \circ \gamma_{Z\left(\mathbf{b}^{1}, \ldots, \mathbf{b}^{m}\right)}
$$
其中，$$H_{\mathbf{a}^{1}, \ldots, \mathbf{a}^{k}}:=h_{\mathbf{a}^{k}} \circ h_{\mathbf{a}^{k-1}} \circ \ldots \circ h_{\mathbf{a}^{1}}$$ 是多个一元函数的复合，$\gamma_{Z\left(\mathbf{b}^{1}, \ldots, \mathbf{b}^{m}\right)}(\mathbf{r})=\max _{\mathbf{x} \in Z\left(\mathbf{b}^{1}, \ldots, \mathbf{b}^{m}\right)}\langle\mathbf{r}, \mathbf{x}\rangle$，$Z$  表示的是一个多面体。这样的一类函数，$\gamma$ 部分用 2 层的 ReLU 网络表示，$H$ 根据之前的定理可以用 $k+1$ 层网络表示，利用函数复合和网络复合的关系，可以证明 $\mathrm{ZONOTOPE}_{k, w, m}^{n}\left[\mathbf{a}^{1}, \ldots, \mathbf{a}^{k}, \mathbf{b}^{1}, \ldots, \mathbf{b}^{m}\right]$ 可以用 $k+2$ 层网络来表示，结点数为 $2m + wk$，$2m$ 和 $\gamma$ 有关，$wk$ 和 $H$ 有关。这类函数的分片数量为 
$$
\left(\sum_{i=0}^{n-1}\left(\begin{array}{c}
m-1 \\
i
\end{array}\right)\right) w^{k}
$$


总结：这些文章，虽然或多或少都涉及到了网络表达的上下界，但是很显然（特别是第一、二、四篇论文），他们讨论的都是特定的网络结构，比如第一篇中隐层的设计 $(x, y+\ell(x))$，第二篇中需要利用 $x^2,xy$ 这些基本函数的逼近，结合泰勒展开，第四篇中需要用到 $\max$ 运算对应的网络结构。对于 $\gamma$ 函数，也是用了特定的网络结构。这些理论讨论设计的网络，和实际应用中的网络有很大的区别。