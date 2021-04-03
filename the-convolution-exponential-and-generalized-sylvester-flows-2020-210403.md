# The Convolution Exponential and Generalized Sylvester Flows

[TOC]

## 信息

Authors: Emiel Hoogeboom, Victor Garcia Satorras, Jakub M. Tomczak, Max Welling

Year: NeurIPS 2020

Bibtex:

```latex
@article{hoogeboom2020convolution,
  title={The convolution exponential and generalized sylvester flows},
  author={Hoogeboom, Emiel and Satorras, Victor Garcia and Tomczak, Jakub M and Welling, Max},
  journal={arXiv preprint arXiv:2006.01910},
  year={2020}
}
```

cite: 7

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/the-convolution-exponential-and-generalized-sylvester-flows.pdf)



## code

https://github.com/ehoogeboom/convolution_exponential_and_sylvester



## 概括

这篇文章提出了 convolution exponential，然后将其用到了 sylvester flow 模型中 convolution exponential 是一个可逆的线性运算，其目的有点类似于 1x1 卷积。只不过使用了卷积来实现。但是一般的卷积是无法保证可逆、计算 log Det Jacobian 的。作者利用了 exp(M) 的形式，M 是一个线性变换。M 实际上是一个卷积运算，因此称为 convolution exponential。exp(M) 的性质是可逆、log Det Jacobian 可计算。逆运算也很简单。由于 exp(M) 是一个级数求和，作者实际只取 6 项，精确度就足够了。

作者将这个方法用于 sylvester flow 模型。首先作者构建了一个更一般的 sylvester 模型（使用了自回归模型），然后将其中的线性变换替换成 convolution exponential。并且还给出这种情形的一些简便的计算方式（但这个部分没有看懂为什么这么算是对的）

作者表示这种方法在 CIFAR-10 的数据上效果超过了其他的生成模型。不过我个人感觉其实效果很一般，即使是指标上看，改进的量根本称不上什么改进。

另外，作者还把这个 convolutional exponential 拓展到 graph 相关的领域，graph normalizing flow 等，但是由于不熟悉相关领域，所以这个部分都没有细看。



## 方法

矩阵的指数化：
$$
\begin{equation}
\exp (\mathbf{M}) \equiv \mathbf{I}+\frac{\mathbf{M}}{1 !}+\frac{\mathbf{M}^{2}}{2 !}+\ldots=\sum_{i=0}^{\infty} \frac{\mathbf{M}^{i}}{i !}
\end{equation}
$$
这个指数化是收敛的，收敛到一个矩阵。而且有很好的性质：
$$
\begin{equation}
\exp (\mathbf{M})^{-1}=\exp (-\mathbf{M}) \quad \text { and } \quad \log \operatorname{det}[\exp (\mathbf{M})]=\operatorname{Tr} \mathbf{M}
\end{equation}
$$
也就是说逆运算和 log det 都很好计算。

如果 M 是一个卷积运算对应的矩阵，那么这个 exp(M) 就变成 convolution exponential。从记号上来说是：
$$
\begin{equation}
\mathbf{z}=\mathbf{m} \star_{e} \mathbf{x}
\end{equation}
$$

$$
\begin{equation}
\mathbf{x} = (-\mathbf{m}) \star_{e} \mathbf{z}
\end{equation}
$$

计算的时候，不可能显式地计算，只能通过迭代算法来计算。而迭代的步数取决于精度的要求，实际上 6 步（也就是计算 （1）中的六项）就够了。下面是算法。由于 M 的矩阵乘法实际上就是卷积运算，所以在算法2中，L 是一个卷积运算。

![屏幕快照 2021-04-03 下午7.58.24](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-03 下午7.58.24.png)

![屏幕快照 2021-04-03 下午7.51.12](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-03 下午7.51.12.png)
$$
\begin{equation}
\mathbf{m} \star_{e} \mathbf{x}=\mathbf{x}+\frac{\mathbf{m} \star \mathbf{x}}{1 !}+\frac{\mathbf{m} \star(\mathbf{m} \star \mathbf{x})}{2 !}+\ldots
\end{equation}
$$
由于 M 是卷积，所以 exp(M) 的矩阵似乎干后也会变大？

这种变化相比较 1x1 卷积而言，似乎更灵活。

接下来介绍作者怎么运用这个技巧到 sylvester flow 中。首先简单说明 sylvester flow。

这个模型利用了 $\begin{equation}
\operatorname{det}(\mathbf{I}+\mathbf{A} \mathbf{B})=\operatorname{det}(\mathbf{I}+\mathbf{B} \mathbf{A})
\end{equation}$，设计出来的变换形式是：
$$
\begin{equation}
\mathbf{z}=\mathbf{x}+\mathbf{A} h(\mathbf{B} \mathbf{x}+\mathbf{b})
\end{equation}
$$
h 可能是一个可逆变换？这个不清楚。在原来的方法中，A 和 B 都是用正交矩阵 Q 和两个不同的三角矩阵构成。$\begin{equation}
\mathbf{A}=\mathbf{Q}^{\mathrm{T}} \mathbf{R} \text { and } \mathbf{B}=\mathbf{R Q}
\end{equation}$. 作者提出的一般化模型是：
$$
\begin{equation}
\mathbf{z}=\mathbf{x}+\mathbf{W}^{-1} f_{\mathrm{AR}}(\mathbf{W} \mathbf{x})
\end{equation}
$$
$f_{AR}$ 是自回归模型。此时，计算 Det 就变成：
$$
\begin{equation}
\operatorname{det}\left(\frac{\mathrm{d} \mathbf{z}}{\mathrm{d} \mathbf{x}}\right)=\operatorname{det}\left(\mathbf{I}+\mathbf{J}_{f_{\mathrm{AR}}}(\mathbf{W} \mathbf{x}) \mathbf{W} \mathbf{W}^{-1}\right)=\operatorname{det}\left(\mathbf{I}+\mathbf{J}_{f_{\mathrm{AR}}}(\mathbf{W} \mathbf{x})\right)
\end{equation}
$$
$J_{f_{AR}}$ 是 $f_{AR}$ 的 Jacobian 矩阵。作者给出的定理是只要 $f_{AR}(x)_i$ 关于 $x_i$ 的偏导数大于 1，(7) 就是可逆的。另一定理是说 (6) 是 (7) 的特例。

作者的改进就是在这个一般话的 sylvester flow 变换下，将 W 用 convolution exponential 和一个正交矩阵表示。计算逆的时候，作者还给了一个不动点的计算方式（不过我觉得这个不太重要，因为我也不会用这个方法，论文的4.1节有详细的说明）主要的好处是，计算自回归的时候可以快一些。正常情况下，f 是很不好计算逆运算的。具体来说，作者的这个自回归模型采用了一个很复杂的形式，但是不知道这个形式的意义是什么：
$$
\begin{equation}
f_{\mathrm{AR}}(\mathbf{u})=\gamma \cdot s_{2}(\mathbf{u}) \odot \tanh \left(\mathbf{u} \odot s_{1}(\mathbf{u})+t_{1}(\mathbf{u})\right)+t_{2}(\mathbf{u})
\end{equation}
$$
这里的所有参数网络都是自回归模型。

总结：形式为
$$
\begin{equation}
\mathbf{z}=\mathbf{x}+\mathbf{Q}^{\mathrm{T}}\left((-\mathbf{m}) \star_{e} f_{\mathrm{AR}}\left(\mathbf{m} \star_{e} \mathbf{Q} \mathbf{x}\right)\right)
\end{equation}
$$
行列式计算为
$$
\begin{equation}
\operatorname{det}\left(\frac{\mathrm{d} \mathbf{z}}{\mathrm{d} \mathbf{x}}\right)=\operatorname{det}\left(\mathbf{I}+\mathbf{J}_{f_{\mathrm{AR}}}\left(\mathbf{m} \star_{e} \mathbf{Q} \mathbf{x}\right)\right)
\end{equation}
$$
由于这个矩阵是三角的，所以比较好算。

实验中，对于 M 矩阵的初始化，采用了非常小的值，这样 exp(M) 就近似于一个 identity 矩阵。实验的优势非常小，我觉得看不出来改进明显。



## 参考文献

这里首先做一个梳理，就是关于不同的变换形式

coupling layer 是 NICE，real NVP 两篇。

自回归有 IAF，MAF，还有

Germain, M., Gregor, K., Murray, I., and Larochelle, H. (2015). Made: Masked autoencoder for distribution estimation. In International Conference on Machine Learning, pages 881–889. 这篇本来也要看，因为 IAF，MAF 用的都是这篇。

Chen, T. Q., Rubanova, Y., Bettencourt, J., and Duvenaud, D. K. (2018). Neural ordinary differential equations. In Advances in Neural Information Processing Systems, pages 6572–6583.

De Cao, N., Aziz, W., and Titov, I. (2019). Block neural autoregressive ﬂow. In Proceedings of the Thirty-Fifth Conference on Uncertainty in Artiﬁcial Intelligence, UAI, page 511.

Song, Y., Meng, C., and Ermon, S. (2019). Mintnet: Building invertible neural networks with masked convolutions. In Advances in Neural Information Processing Systems 32: Annual Conference on Neural Information Processing Systems 2019, NeurIPS 2019.

Nielsen, D. and Winther, O. (2020). Closing the dequantization gap: Pixelcnn as a single-layer ﬂow. CoRR, abs/2002.02547.

spine flow 这个没听说过

Durkan, C., Bekasov, A., Murray, I., and Papamakarios, G. (2019b). Neural spline ﬂows. In Advances in Neural Information Processing Systems, pages 7509–7520.

polynomial ﬂows 听过没看过

Jaini, P., Selby, K. A., and Yu, Y. (2019). Sum-of-squares polynomial ﬂow. In Proceedings of the 36th International Conference on Machine Learning, ICML 2019, 9-15 June 2019, Long Beach, California, USA, volume 97 of Proceedings of Machine Learning Research, pages 3009–3018. PMLR.

sylvester flow

van den Berg, R., Hasenclever, L., Tomczak, J. M., and Welling, M. (2018). Sylvester normalizing ﬂows for variational inference. In 34th Conference on Uncertainty in Artiﬁcial Intelligence 2018, UAI 2018, pages 393–402.

residual flow

Behrmann, J., Grathwohl, W., Chen, R. T. Q., Duvenaud, D., and Jacobsen, J. (2019). Invertible residual networks. In Proceedings of the 36th International Conference on Machine Learning, ICML 2019, 9-15 June 2019, Long Beach, California, USA, volume 97 of Proceedings of Machine Learning Research, pages 573–582. PMLR.

Chen, T. Q., Behrmann, J., Duvenaud, D., and Jacobsen, J. (2019). Residual ﬂows for invertible generative modeling. In Advances in Neural Information Processing Systems 32: Annual Conference on Neural Information Processing Systems 2019, NeurIPS 2019, 8-14 December 2019, Vancouver, BC, Canada, pages 9913–9923.

其他的参考

Karami, M., Schuurmans, D., Sohl-Dickstein, J., Dinh, L., and Duckworth, D. (2019). Invertible convolutional ﬂow. In Advances in Neural Information Processing Systems 32: Annual Conference on Neural Information Processing Systems 2019, NeurIPS 2019, 8-14 December 2019, Vancouver, BC, Canada, pages 5636–5646.

Goli´nski, A., Lezcano-Casado, M., and Rainforth, T. (2019). Improving normalizing ﬂows via better orthogonal parameterizations. Workshop on Invertible Neural Nets and Normalizing Flows.