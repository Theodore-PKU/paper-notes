### Paper:

Learning a Compressed Sensing Measurement Matrix via Gradient Unrolling

### Author:

Shanshan Wu, Alexandros G. Dimakis, Sujay Sanghavi, Felix X. Yu, Daniel Holtmann-Rice, Dmitry Storcheus, Afshin Rostamizadeh, Sanjiv Kumar

### Year:

2019

### Notes:

阅读时间：2020.01.30

泛读。这篇文章研究的是如何学习一个测量矩阵，作者考虑的是信号的稀疏性也具有某种结构，测量矩阵如果可以利用这些结构会使得重建所需要的测量值更少，但是信号的稀疏结构各自不相同，所以要用学习的方式获得。使用的方法概括起来就是用一个 unroll 形式的投影算法和测量矩阵构成一个网络的形式，矩阵运算都变成全连接层。作者在这篇文章中唯一能看出亮点的是将原本的投影算法利用一个引理使其可以简单的用神经网络来表示（这里作者是不严格的替换，但是作者表示其结果基本满足条件），并且很容易进行反向传播求导。另外，unroll 的算法中的每一个 block 的步长是可学习的，作者表示，实验结果显示这些步长和传统算法中的步长也非常接近。作者在论文说学习到的测量矩阵，比高斯随机矩阵的压缩感知率提高很多。

但是，在实验方面，作者用的是文本、分类变量这种本身非常稀疏的数据，数据的维度也不高，因此全连接层也没问题，不过就和图像无关了。这里的信号或者说数据的结构就变得比较简单。作者提出的方法就变成对这些数据的压缩感知形式的重建的探讨，感觉没有什么特别的意义。（也能发ICML？）由于这个实验内容和图像完全无关，采用的学习测量矩阵的策略也没有什么特别的新意（只不过是用 unroll 的网络结构重建罢了，之前很多文章用别的结构重建了），因此泛读，实验和结论都略过。

下面就将投影算法和作者使用的引理进行说明。

原问题：

<img src="http://latex.codecogs.com/svg.latex? D(A, y):=\arg \min _{x^{\prime} \in \mathbb{R}^{d}}\left\|x^{\prime}\right\|_{1} \quad \text { s.t. } A x^{\prime}=y" border="0"/>

直接对矩阵 A 求导不现实：

<img src="http://latex.codecogs.com/svg.latex? \min _{A \in \mathbb{R}^{m \times d}} f(A), \text { where } f(A):=\sum_{i=1}^{n}\left\|x_{i}-D\left(A, A x_{i}\right)\right\|_{2}^{2}" border="0"/>

如果将 $D(A,Ax_i)$ 替换为 T-step 的迭代算法就可以了。

<img src="http://latex.codecogs.com/svg.latex? x^{(t+1)}=\Pi\left(x^{(t)}-\alpha_{t} g^{(t)}\right), \text { where } g^{(t)}=\operatorname{sign}\left(x^{(t)}\right)" border="0"/>

其中，$\Pi$ 表示投影到 $\left\{x^{\prime}: A x^{\prime}=y\right\}$

经过计算，得到迭代式子：

<img src="http://latex.codecogs.com/svg.latex? x^{(t+1)}=x^{(t)}-\alpha_{t}\left(I-A^{\dagger} A\right) \operatorname{sign}\left(x^{(t)}\right)" border="0"/>

这里的伪逆 $A^{\dagger}$ 在网络中无法表示。所以作者利用了引理：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{ll}{P_{1}:} & {\min _{x^{\prime} \in \mathbb{R}^{d}}\left\|x^{\prime}\right\|_{1} \quad \text { s.t. } A x^{\prime}=A x} \\ {P_{2}:} & {\min _{x^{\prime} \in \mathbb{R}^{d}}\left\|x^{\prime}\right\|_{1} \quad \text { s.t. } \tilde{A} x^{\prime}=\tilde{A} x}\end{array}" border="0"/>

总是存在一个奇异值全为1的矩阵 $\tilde{A}$ 可以有和 $A$ 相同的解，对于这个矩阵，不需要用伪逆，只需要用转置就可以计算。

<img src="http://latex.codecogs.com/svg.latex? x^{(t+1)}=x^{(t)}-\alpha_{t}\left(I-\tilde{A}^{T} \tilde{A}\right) \operatorname{sign}\left(x^{(t)}\right), \quad x^{(1)}=\tilde{A}^{T} \tilde{A} x" border="0"/>

因此，作者实际在网络中用转置而不是伪逆，作者在论文中说，按理需要对 $A$ 的奇异值有限制，但是作者在实践中发现 ，没有限制的训练结果，也基本满足这一条件 (is not far from the constraint set)。

另外在网络中，作者还加了 BN 层，不知道有什么用（只是效果好），最后一层用了一个 ReLU 保证重建结果符合非负约束。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-30%E4%B8%8A%E5%8D%8811.58.17.png"/>

### Bibtex:

```latex
@article{wu2018learning,
  title={Learning a compressed sensing measurement matrix via gradient unrolling},
  author={Wu, Shanshan and Dimakis, Alexandros G and Sanghavi, Sujay and Yu, Felix X and Holtmann-Rice, Daniel and Storcheus, Dmitry and Rostamizadeh, Afshin and Kumar, Sanjiv},
  journal={arXiv preprint arXiv:1806.10175},
  year={2018}
}
```

