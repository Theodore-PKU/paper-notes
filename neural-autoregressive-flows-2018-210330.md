# Neural Autoregressive Flows

[TOC]

## 信息

Authors: Chin-Wei Huang, David Krueger, Alexandre Lacoste, Aaron Courville

Year: ICML 2018

Bibtex:

```
@inproceedings{huang2018neural,
  title={Neural autoregressive flows},
  author={Huang, Chin-Wei and Krueger, David and Lacoste, Alexandre and Courville, Aaron},
  booktitle={International Conference on Machine Learning},
  pages={2078--2087},
  year={2018},
  organization={PMLR}
}
```

cite: 191

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/neural-autoregressive-flows.pdf)



## 概括

这篇文章的主要贡献是提出了一个新的可逆函数的形式，和此前的 affine 形式的可逆函数不同。题目中的 autogressive 指的是类似于 IAF, MAF 模型，这篇文章也通过自回归的形式构造函数，使得 Jacobian 矩阵是可计算的。这里自回归的方法应该都是说 x_i 的相关参数和 x_i 之前的分量有关，类似于很多自回归模型。

在 flow 模型中，主要的核心是可逆函数的形式。作者称自己的方法为 NAF，是因为 x_i 之前的分量回归的结果是 MLP 网络参数，而这些网络参数用于 x_i 的相关变换。作者考虑了两种不同的 MLP 结构 DSF 和 DDSF。我认为这是这篇文章的重点，但是实际在论文中，作者还对 flow 模型的所谓 structure 到 un-structure 模型、un-structure 到 structure 模型什么的进行了讨论，我觉得这不是重点。因为 NAF 的方法可以用于任何需要可逆网络的场景。

另外，作者还给出了 DSF 的相关分析，说明其在各种场景下都是有效的。不过理论部分没有证明，我也不太关心。



## 方法

这个部分重点说明 NAF 和之前的做法的区别（AAF）

AF 是说自回归 flow，这个部分是指 x 的分量是如何和其他分量产生联系的，因为这个涉及到 Jacobian 矩阵的形式（不过这种方法怎么实现可逆的我不记得了，这不是这篇文章的重点，要参考之前的那几篇文章才行，这个比 split 的方法复杂）。一种做法是 spilt，AF 则是另一种做法，具体来说就是 x_i 取决于 x_i 此前的分量。我觉得下式可以说明这个设计。
$$
\begin{equation}
y_{t} \doteq f\left(x_{1: t}\right)=\tau\left(c\left(x_{1: t-1}\right), x_{t}\right)
\end{equation}
$$
之前的做法是 AAF，第一个 A 指的是 affine，线性的形式。

一种做法是：
$$
\begin{equation}
\tau\left(\mu, \sigma, x_{t}\right)=\mu+\sigma x_{t}
\end{equation}
$$
另一种做法是：
$$
\begin{equation}
\tau\left(\mu, \sigma, x_{t}\right)=\sigma x_{t}+(1-\sigma) \mu
\end{equation}
$$
作者提出的则是：
$$
\begin{equation}
\tau\left(c\left(x_{1: t-1}\right), x_{t}\right)=\operatorname{DNN}\left(x_{t} ; \phi=c\left(x_{1: t-1}\right)\right)
\end{equation}
$$
也就是说用一个神经网络来实现，只不过参数是由其他 x 分量决定。

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-30 下午6.49.54.png" alt="屏幕快照 2021-03-30 下午6.49.54" style="zoom:50%;" />

这个图给出了 NAF 的形式。注意到 c_i 表示的产生全连接网络参数的自回归模型。

DNN 的形式作者给出了两种：

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-30 下午6.50.01.png" alt="屏幕快照 2021-03-30 下午6.50.01" style="zoom:50%;" />

因为这个网络要实现变量之间的变换，作者对激活函数进行了特别的设置，这里交替使用了 sigmoid 函数及其逆函数，目的是保证 x 和 y 的值域的不变性。DDSF 函数的非线性更强一些，因为避免了 bottleneck 的结构。另外，生成的参数 w，作者也有很多限制，作者把它们设计成 attention 的形式，目的在于控制 MLP 网络的可逆性（随便一个 MLP 网络不是可逆的）

下面这个图则很直观的说明了线性和 DNN 的差异。

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-30 下午8.08.44.png" alt="屏幕快照 2021-03-30 下午8.08.44" style="zoom:50%;" />

## 反思

我认为这篇文章有意思的地方在于不用固定形式实现变量变换，而使用神经网络的形式，这说明这方面的设计还是有很大空间的，只要对网络有很好的设置。而且这篇文章的设置有点像 self-attention，猜测可以设计出更好的网络结构。而且这个 DNN 也许就是会融合到 self-attention 中去。



## 参考文献

Huang, C.-W., Touati, A., Dinh, L., Drozdzal, M., Havaei, M., Charlin, L., and Courville, A. Learnable explicit density for continuous latent space and variational inference. arXiv preprint arXiv:1710.02248, 2017.

Kingma, D. P., Salimans, T., Jozefowicz, R., Chen, X., Sutskever, I., and Welling, M. Improved variational inference with inverse autoregressive ﬂow. In Advances in Neural Information Processing Systems, 2016.