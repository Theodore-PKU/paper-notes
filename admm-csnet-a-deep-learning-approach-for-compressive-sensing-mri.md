### Paper:

ADMM-CSNet: A Deep Learning Approach for Compressive Sensing MRI

### Author:

Yan Yang, Jian Sun, Huibin Li, and Zongben Xu

### Year:

2018

### Notes:

精读。作者之前写过另外一篇文章 [Deep admm-net for compressive sensing mri](deep-admm-net-for-compressive-sensing-mri.md)，两者的思路基本是一样的，但是这一篇是改进的版本。这篇笔记会比原来的笔记更详细一些地记录论文的信息。总体思路：将ADMM算法中的每个操作（算子）都替换成可学习的模块。这一篇的改进主要在于提出了另一种ADMM算法的求解方式，因此对应另一种ADMM-Net，考虑了图像是复数值时的处理手段，以及更多的对比实验。（虽然实验结果上，这篇论文的表现比其他方法好，但是引用率不高。个人感觉比较的其他方法不算最先进的或者相应论文写得并不仔细，这篇是18年发表，17年出的第一稿）。

下面的内容会按照以下逻辑进行：1.  ADMM算法解法1以及对应的basic ADMM-net；2. ADMM算法解法2以及对应的generic ADMM-net；3.  训练和实验

压缩感知重建总的目标函数：
$$
\hat{x}=\underset{x}{\arg \min }\left\{\frac{1}{2}\|A x-y\|_{2}^{2}+\sum_{l=1}^{L} \lambda_{l} g\left(D_{l} x\right)\right\}
$$

#### ADMM算法解法1以及对应的basic ADMM-net

**ADMM solver I**

优化问题：
$$
\begin{array}{l}
{\min _{x} \frac{1}{2}\|A x-y\|_{2}^{2}+\sum_{l=1}^{L} \lambda_{l} g\left(z_{l}\right)} \\
{\text { s.t. } z_{l}=D_{l} x, \quad \forall l \in\{1,2, \cdots, L\}}
\end{array}
$$
求解算法：
$$
\left\{\begin{aligned}
\mathbf{X}^{(\mathbf{n})}: x^{(n)} &=F^{T}\left[P^{T} P+\sum_{l=1}^{L} \rho_{l} F D_{l}^{T} D_{l} F^{T}\right]^{-1} \\
&\left[P^{T} y+\sum_{l=1}^{L} \rho_{l} F D_{l}^{T}\left(z_{l}^{(n-1)}-\beta_{l}^{(n-1)}\right)\right] & \\
\mathbf{Z}^{(\mathbf{n})}: z_{l}^{(n)} &=\mathcal{S}\left(D_{l} x^{(n)}+\beta_{l}^{(n-1)} ; \lambda_{l} / \rho_{l}\right) \\
\mathbf{M}^{(\mathbf{n})}: \beta_{l}^{(n)} &=\beta_{l}^{(n-1)}+\eta_{l}\left(D_{l} x^{(n)}-z_{l}^{(n)}\right)
\end{aligned}\right.
$$
**Basic-ADMM-Net**

Reconstruction layer ($\mathbf{X}^{(\mathbf{n})}$): 给定 $z_{l}^{(n-1)} \text { and } \beta_{l}^{(n-\overline{1})}$
$$
\begin{aligned}
x^{(n)}=& F^{T}\left(P^{T} P+\sum_{l=1}^{L} \rho_{l}^{(n)} F H_{l}^{(n) T} H_{l}^{(n)} F^{T}\right)^{-1} \\
&\left[P^{T} y+\sum_{l=1}^{L} \rho_{l}^{(n)} F H_{l}^{(n) T}\left(z_{l}^{(n-1)}-\beta_{l}^{(n-1)}\right)\right]
\end{aligned}
$$
其中，可学习的参数是 $H_l^{(n)}$（$w_{f} \times w_{f}$ 的滤波器）和  $\rho_l^{(n)}$（惩罚系数）。当 $n=1$ 时，$z_l^{(0)}$ 和 $\beta_l^{(0)}$ 都是0.

Convolution layer ($\mathbf{C}^{(\mathbf{n})}$): 执行的 $\mathbf{Z}^{(\mathbf{n})}$ 中的 $D_{l} x^{(n)}$，
$$
c_{l}^{(n)}=D_{l}^{(n)} x^{(n)}
$$
其中可学习参数 $D_{l}^{(n)}$ 是$w_{f} \times w_{f}$ 的滤波器对应的矩阵。这里需要注意的是，$D_{l}^{(n)}$ 和 $H_l^{(n)}$ 不相同，而在ADMM算法中它们是一样的。

Nonlinear transform layer ($\mathbf{S}^{(\mathbf{n})}$)：shrinkage function，计算出 $\mathbf{Z}^{(\mathbf{n})}$ 的结果，给定 $c_l^{(n)}, \beta_l^{(n)}$
$$
z_{l}^{(n)}=S_{P L F}\left(c_{l}^{(n)}+\beta_{l}^{(n-1)} ;\left\{p_{i}, q_{l, i}^{(n)}\right\}_{i=1}^{N_{c}}\right)
$$
$S_{P L F}(\cdot)$ 是分段线性函数，由可学习参数 $\left\{p_{i}, q_{l, i}^{(n)}\right\}_{i=1}^{N_{c}}$ 控制：
$$
S_{P L F}(a)=\left\{\begin{array}{ll}
{a+q_{l, 1}^{(n)}-p_{1},} & {a<p_{1}} \\
{a+q_{l, N_{c}}^{(n)}-p_{N_{c}},} & {a>p_{N_{c}}} \\
{q_{l, r}^{(n)}+\frac{\left(a-p_{r}\right)\left(q_{l, r+1}^{(n)}-q_{l, r}^{(n)}\right)}{p_{r+1}-p_{r}},} & {p_{1} \leq a \leq p_{N_{c}}}
\end{array}\right.
$$
Multiplier update layer ($\mathbf{M}^{(\mathbf{n})}$): 给定 $\beta_{l}^{(n-1)}, c_{l}^{(n)} \text { and } z_{l}^{(n)}$，计算
$$
\beta_{l}^{(n)}=\beta_{l}^{(n-1)}+\eta_{l}^{(n)}\left(c_{l}^{(n)}-z_{l}^{(n)}\right)
$$
一个计算例子，序号表示计算的顺序：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-01%E4%B8%8A%E5%8D%8810.15.25.png" style="zoom: 50%;" />

#### ADMM算法解法2以及对应的generic ADMM-net

**ADMM solver II**

优化问题：
$$
\begin{array}{l}
{\min _{x, z} \frac{1}{2}\|A x-y\|_{2}^{2}+\sum_{l=1}^{L} \lambda_{l} g\left(D_{l} z\right)} \\
{\text { s.t. } z=x}
\end{array}
$$
求解算法：(注意，这里的 $\mathbf{Z}^{(\mathbf{n})}$ 本身是一个多次迭代，所以用 $k$ 来表示内部迭代)
$$
\left\{\begin{aligned}
\mathbf{X}^{(\mathbf{n})}: x^{(n)} &=F^{T}\left(P^{T} P+\rho I\right)^{-1} \\
&\left[P^{T} y+\rho F\left(z^{(n-1)}-\beta^{(n-1)}\right)\right] & \\
\mathbf{Z}^{(\mathbf{n})}: z^{(n, k)} &=\mu_{1} z^{(n, k-1)}+\mu_{2}\left(x^{(n)}+\beta^{(n-1)}\right) \\
&-\sum_{l=1}^{L} \tilde{\lambda}_{l} D_{l}^{T} \mathcal{H}\left(D_{l} z^{(n, k-1)}\right) \\
\mathbf{M}^{(\mathbf{n})}: \beta^{(n)} &=\beta^{(n-1)}+\tilde{\eta}\left(x^{(n)}-z^{(n)}\right)
\end{aligned}\right.
$$
Reconstruction layer ($\mathbf{X}^{(\mathbf{n})}$)：
$$
\begin{aligned}
x^{(n)}=& F^{T}\left(P^{T} P+\rho^{(n)} I\right)^{-1} \\
&\left[P^{T} y+\rho^{(n)} F\left(z^{(n-1)}-\beta^{(n-1)}\right)\right]
\end{aligned}
$$
可学习参数只有 $\rho^{(n)}$.

对于 $\mathbf{Z}^{(\mathbf{n})}$，分解为三步。

Addition layer ($\mathbf{A}^{(\mathbf{n}, \mathbf{k})}$)：给定 $x^{(n)}, \beta^{(n-1)}, z^{(n, k-1)},c_2^{(n,k-1)}$,
$$
z^{(n, k)}=\mu_{1}^{(n, k)} z^{(n, k-1)}+\mu_{2}^{(n, k)}\left(x^{(n)}+\beta^{(n-1)}\right)-c_{2}^{(n, k)}
$$
$\mu_{1}^{(n, k)} \text { and } \mu_{2}^{(n, k)}$ 是可学习参数，要求两者只和为 1。$z^{(n, 0)}$ 初始化为 $x^{(n)}+\beta(n-1)$。

Convolution layer ($\mathbf{C}_{\mathbf{1}}^{(\mathbf{n}, \mathbf{k})}, \mathbf{C}_{2}^{(\mathbf{n}, \mathbf{k})}$)：两个卷积运算中间还有一个运算 $\mathbf{H}$，计算出 $h^{(n,k)}$
$$
\begin{aligned}
c_{1}^{(n, k)} &=w_{1}^{(n, k)} * z^{(n, k-1)}+b_{1}^{(n, k)} \\
c_{2}^{(n, k)} &=w_{2}^{(n, k)} * h^{(n, k)}+b_{2}^{(n, k)}
\end{aligned}
$$
可学习参数 $w_1^{(n,k)}$ 是L个 $w_{f} \times w_{f}$ 的滤波器，。所以 $c_1^{(n,k)}$ 是一个L通道的feature map，$b_i^{(n,k)}$ 是相应的偏置。$w_2^{(n,k)}$ 则把L通道的输入变成1通道。

Nonlinear transform layer ($\mathbf{H}^{(\mathbf{n}, \mathbf{k})}$)：和 $\mathbf{S}^{(\mathbf{n})}$ 类似,
$$
h^{(n, k)}=S_{P L F}\left(c_{1}^{(n, k)} ;\left\{p_{i}, q_{i}^{(n, k)}\right\}_{i=1}^{N_{c}}\right)
$$
Multiplier update layer ($\mathbf{M}^{(\mathbf{n})}$): 和 basic ADMM-net 一样。

一个计算例子，序号表示计算的顺序：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-01%E4%B8%8A%E5%8D%8810.16.55.png" style="zoom:50%;" />

**对于复数值的处理**

For the convolution layers, we still use real-valued ﬁlters to convolve complex-valued data. It means that the ﬁlters in convolution layers are all real-valued, and the convolution is performed between the real-valued ﬁlters and complexvalued inputs. This is equivalently to performing convolution over real and imagery parts of layer input separately using shared real-valued ﬁlters. For the nonlinear transform layer, we deal with real and imaginary parts separately
$$
h^{(n, k)}=S_{P L F}\left(\mathbf{R} \mathrm{e}\left(c_{1}^{(n, k)}\right)\right)+j S_{P L F}\left(\mathbf{I m}\left(c_{1}^{(n, k)}\right)\right)
$$

#### 训练和试验

损失函数为 NMSE：
$$
E(\Theta)=\frac{1}{|\Gamma|} \sum_{\left(y, x^{g}\right) \in \Gamma} \frac{\left\|\hat{x}(y, \Theta)-x^{g t}\right\|_{2}}{\left\|x^{g t}\right\|_{2}}
$$
优化算法为 L-BFGS。

网络的可学习参数初始化有两种方式。一种是按照传统的 DCT 变换下的卷积参数，shrinkage 函数是 soft thresholding function，这样初始网络就是一个传统的ADMM算法。一种是高斯随机初始化卷积参数，shrinkage 函数用的是 ReLU 的设定。

论文中还讨论导数的计算。

实验：

- 图像大小：256x256
- 训练：100张；测试：50张 
- 采样率：10%，20%，30%，40%
- 采样方式：径向采样
- 评价指标：NMSE，PSNR
- 比较了  basic 和 generic 两种网络的差别（在给定基本一样的条件下）：generic 表现更好，收敛更快
- 不同初始化的效果：差不太多
- 比较了 stage的个数、每个stage里的滤波器个数、大小的影响：总的来说 stage 越大，滤波器越多，大小越大效果更好，但是计算量相应提升，并且效果提升逐渐收敛有限。（没说用的是 generic 还是其他的，但估计是generic）
- 和其他方法比较了训练时间和效果：论文的方法效果好一点，训练时间短。
- 探究了泛化性能：很不错，用大脑的MRI数据集训练再胸部数据集上表现很好。

> 思考：这篇论文的 generic ADMM-net 比 basic 版本在 $\mathbf{Z}$ 模块上更接近于神经网络。

### Bibtex:

```
@article{yang2018admm,
  title={ADMM-CSNet: A Deep Learning Approach for Image Compressive Sensing},
  author={Yang, Yan and Sun, Jian and Li, Huibin and Xu, Zongben},
  journal={IEEE transactions on pattern analysis and machine intelligence},
  year={2018},
  publisher={IEEE}
}
```