### Paper:

Breaking the Coherence Barrier: A New Theory for Compressed Sensing

### Author:

Ben Adcock, Anders C. Hansen, Clarice Poon, Dogdan Roman

### Year:

2018

### Notes:

阅读日期：2020.01.16

精读。这篇文章很长，将按照论文的章节顺序记笔记。

#### Abstract

这篇文章想建立一个新的压缩感知框架，并引入一个新的采样方法。作者首先表示在很多压缩感知的应用领域，标准的准则之一 incoherence 和 sparsity 是不满足的。但是仍然有成功的案例，这些应用缺乏好的数学解释。作者试图对标准的压缩感知理论进行一些调整。具体来说，要用更一般的概念来替换标准的准则。asymptotic sparsity，asymptotic incoherence，multilevel random subsampling。作者认为，最优的采样策略不仅取决于 sampling operator 的 incoherence structure，还有信号本身的 structure。

#### 1 Introduction

In many of these problems, it is the principle of incoherence that is lacking, rendering the standard theory inapplicable. And it is typically implemented with sampling strategies that differ substantially from the uniform random sampling strategies suggested by the theory.

In the above applications one also witnesses a number of intriguing phenomena that are not explained by the standard theory. For example, in such problems, the optimal sampling strategy depends not just on the overall sparsity of the signal, but also on its structure.

##### 1.1 Contributions

The purpose of this paper is to provide a bridge across this divide. It does so by introducing a theoretical framework for CS based on three more general principles: asymptotic sparsity, asymptotic incoherence and multilevel random sampling.

这种一般化的重要性在于：很多逆问题是 asymptiotically incoherent, asymptotically sparse; 设计新的感知机制。

作者指出，很多应用（比如 MRI）有限定的测量算子，在很多流行的稀疏基下是 coherent，但是他们是 asymptotically incoherent.

#### 2 Motivation

**Incoherence is often lacking**

矩阵的 coherence 定义：

**definition 2.1**: 令 $U = (u_{ij})_{i,j=1}^{N}\in\mathbb{C}^{N\times N}$ 是 isometry，coherence of $U$ 为：

<img src="http://latex.codecogs.com/svg.latex? \mu(U)=\max _{i, j=1, \ldots, N}\left|u_{i j}\right|^{2} \in\left[N^{-1}, 1\right]" border="0"/>

如果 $\mu(U) = N^{-1}$, 则称 $U$ 是 perfectly incoherence。

以傅立叶变换和小波变换为例， $U_N = U_{df}V^{-1}_{dw}$ 的 coherence 为 $\mathcal{O}(1)$ 当 $K\rightarrow\infin$. 因此很多情况下 incoherence 的条件不满足。

**稀疏性，flip test 和 RIP**

flip test 指的是如下的测试。令 $x\in\mathbb{C}^N, U\in\mathbb{C}^{N\times N}$，选择 $U$ 的某些哈那个，作为测量，求解优化问题：

<img src="http://latex.codecogs.com/svg.latex? \min _{z \in \mathbb{C}^{N}}\|z\|_{1} \text { subject to } P_{\Omega} U z=P_{\Omega} U x" border="0"/>

对 $x$ 进行翻转，每个分量调换位置得到 $x^{fp}$，对应另一个优化问题，两个问题的解 $z_1, z_2$ 不满足翻转关系（预期应该是满足）。

If sparsity alone is the key structure that determines such reconstruction quality, then we expect roughly the same quality in the approximation z 2 , since x fp is merely a permutation of x.

We therefore conclude that sparsity alone does not govern the reconstruction quality, and that the successful recovery in the unﬂipped case must also be due in part to the structure of the signal. Put another way: the best subsampling strategy depends on the signal structure.

The ﬂip test also reveals another interesting phenomenon: the RIP does not hold.

作者做了一些实验，如下图，可以看到重建的结果差异较大。

#### <img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-16%E4%B8%8B%E5%8D%883.03.02.png"/>

#### 3 New principles

这一小节，作者给出了三个新的 principle

**Asymptotic incoherence**

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-16%E4%B8%8B%E5%8D%883.06.15.png"/>

简单地理解就是矩阵 $U$ 的子矩阵是趋向于满足 incoherence 的特性。一些实例：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-16%E4%B8%8B%E5%8D%883.10.55.png"/>

**Multilevel sampling**

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-16%E4%B8%8B%E5%8D%883.14.46.png"/>

multilevel sampling 可以理解在采样是分段的或不均匀的。

**Asymptotic sparsity in levels**

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-16%E4%B8%8B%E5%8D%883.13.51.png"/>

sparsity in levels 指的是稀疏性也是分段的。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-16%E4%B8%8B%E5%8D%883.09.49.png"/>

这三个新的 principle 其实有一个内在的一致性，也就是分段或分level。这也许和多尺度有很强的关联。根据这三个 principle，最优的采样方式，甚至是重建方式，以及深度学习的设计都有新的指导性。



在考虑这种 不同 level 的 sparsity 和 coherence 情况下，假设采样使用了 multilevel sampling，就会出现采样的 level 和信号本身结构的 level 不一致的情况，这也就会导致重建算法的误差。在后面的章节中，作者则给出了相应的误差上界。在此不赘述

### Bibtex:

```latex
@inproceedings{adcock2017breaking,
  title={Breaking the coherence barrier: A new theory for compressed sensing},
  author={Adcock, Ben and Hansen, Anders C and Poon, Clarice and Roman, Bogdan},
  booktitle={Forum of Mathematics, Sigma},
  volume={5},
  year={2017},
  organization={Cambridge University Press}
}
```

