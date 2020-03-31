### Paper:

Dynamic PET Image Reconstruction Using Nonnegative Matrix Factorization Incorporated With Deep Image Prior

### Author:

Tatsuya Yokota, Kazuya Kawai, Muneyuki Sakata, Yuichi Kimura, and Hidekata Hontani

### Year:

2019

### Notes:

泛读。这篇文章研究的是动态 PET 重建。作者的想法是 DIP 和 low-rank 矩阵分解的想法结合起来，使用两个 prior 构建重建相关的目标函数，然后用交替方向方法进行求解。DIP 是空间上的表示，low-rank 是时间上的稀疏性表示。具体的结合方式是 DIP 输出的图像是矩阵分解 AB 中的 A 的各个列，类似于基图像。目标函数的优化变量是网络参数和矩阵分解中的另一个矩阵 B。通过对 B 的约束控制时间上的稀疏性。

流程图

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200331151452.png" width="70%"/>

因为是动态 PET，所以模型为：
$$
\boldsymbol{Y}=\boldsymbol{P} \boldsymbol{Z}^{*}+\boldsymbol{N}
$$
因为是实值图像，所以是 nonnegativ matrix factorization，也就是假设：
$$
Z=A B^{T},\boldsymbol{A}=\left[\boldsymbol{a}_{1}, \ldots, \boldsymbol{a}_{R}\right] \in \mathbb{R}_{+}^{N_{\mathrm{i}} \times R},\boldsymbol{B}=\left[\boldsymbol{b}_{1}, \ldots, \boldsymbol{b}_{R}\right] \in \mathbb{R}_{+}^{T \times R}
$$
这篇文章的想法就是 A 的分量用多个 U-net DIP 表示，B 用来控制时间上的稀疏性（对 B 进行约束）。

目标函数：
$$
\begin{aligned}
&\begin{aligned}
\underset{\Theta, \boldsymbol{B}}{\operatorname{minimize}} \mathcal{L} &:=D_{\mathrm{KL}}\left(\boldsymbol{Y} \| \boldsymbol{P} \boldsymbol{A} \boldsymbol{B}^{T}\right) \\
&+\alpha\left\|\boldsymbol{A}^{T}\right\|_{p, 2}^{2}+\beta\|\boldsymbol{B}\|_{\mathrm{QV}}^{2}
\end{aligned}\\
&\text { s.t. } \quad \boldsymbol{A}=\left[\boldsymbol{a}_{1}, \ldots, \boldsymbol{a}_{R}\right] \geq 0, \boldsymbol{B} \geq 0\\
&\boldsymbol{a}_{r}=\boldsymbol{\phi}\left(u | \theta_{r}\right) \in[0,1]^{N_{i}}\\
&\left\|\boldsymbol{a}_{r}\right\|_{\infty}=1 \text { for } r=1,2, \ldots, R
\end{aligned}
$$
需要注意的有：

$D_{\mathrm{KL}}\left(\boldsymbol{Y} \| \boldsymbol{P} \boldsymbol{A} \boldsymbol{B}^{T}\right)$ 表示的是 data consistency，也就是 $D_{\mathrm{KL}}(\boldsymbol{p} \| \boldsymbol{q}):=\sum_{i} p_{i} \log \left(p_{i} / q_{i}\right)$，用来控制 y 和估计的测量值之间的分布要接近。

$\boldsymbol{\phi}\left(u | \theta_{r}\right)$ 是 DIP 的网络。

$\|\boldsymbol{B}\|_{\mathrm{QV}}:=\|\boldsymbol{\nabla} \boldsymbol{B}\|_{F}^{2}$，其中
$$
\boldsymbol{L}:=\left(\begin{array}{ccccc}
1 & -1 & 0 & \cdots & 0 \\
0 & 1 & -1 & \cdots & 0 \\
\vdots & \vdots & \ddots & \ddots & \vdots \\
0 & \cdots & \cdots & 1 & -1
\end{array}\right) \in \mathbb{R}^{(T-1) \times T}
$$
这是用来控制时间上的稀疏性。

优化算法方面，网络参数的优化是 Adam 优化器，B 的优化是（$\cdot \mu_B$ 表示 element-wise power，$\oslash$ 表示 element-wise 除）
$$
\boldsymbol{B} \leftarrow \boldsymbol{B} \oplus\left[\partial \boldsymbol{B}_{-} \oslash \partial \boldsymbol{B}_{+}\right]^{\cdot \mu_{B}}\\
\begin{array}{l}
\partial \boldsymbol{B}_{-}=\left(\boldsymbol{Y} \oslash \boldsymbol{P} \boldsymbol{A} \boldsymbol{B}^{T}\right)^{T} \boldsymbol{P} \boldsymbol{A}+\beta \max (-\boldsymbol{H} \boldsymbol{B}, 0) \\
\partial \boldsymbol{B}_{+}=\mathbf{1}_{T \times N_{\mathrm{s}}} \boldsymbol{P} \boldsymbol{A}+\beta \max (\boldsymbol{H} \boldsymbol{B}, 0), \boldsymbol{H}:=\boldsymbol{L}^{T} \boldsymbol{L}
\end{array}
$$
作者表示这种优化方法在很多 nonnegative matrix factorization 中使用。

### Bibtex:

```latex
@inproceedings{yokota2019dynamic,
  title={Dynamic PET Image Reconstruction Using Nonnegative Matrix Factorization Incorporated with Deep Image Prior},
  author={Yokota, Tatsuya and Kawai, Kazuya and Sakata, Muneyuki and Kimura, Yuichi and Hontani, Hidekata},
  booktitle={Proceedings of the IEEE International Conference on Computer Vision},
  pages={3126--3135},
  year={2019}
}
```

### 其他

