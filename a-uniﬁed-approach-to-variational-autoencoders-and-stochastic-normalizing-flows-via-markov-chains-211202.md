A Uniﬁed Approach to Variational Autoencoders and Stochastic Normalizing Flows via Markov Chains

[TOC]

## 信息

作者：Johannes Hertrich

年份：2021

期刊会议：arXiv

Bibtex：暂无

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/a-uniﬁed-approach-to-variational-autoencoders-and-stochastic-normalizing-flows-via-markov-chains.pdf)



## 概括

通读。

但这篇文章没有方法的介绍。

这篇文章首先介绍了马尔可夫链的数学概念，作者用的数学语言非常复杂，我只能用最简单的概率论只是来理解。马尔可夫链就是随机变量的状态转移，相邻的两个随机状态对应的随机变量的维度可以是不同的。作者用马尔可夫链来解释 normalizing flow，stochastic normalizing flow 和 vae。

对于 normalizing flow相当于状态转移分布是一个 Dirac 分布。因此是 determined 状态转移。

对于 stochastc normalizing flow，则是前者的推广，即将 Dirac 分布替换成一个一般的分布。由于一般的分布不是 determined 的，而马尔可夫链就要求能够对 x_t | x_{t+1} 采样。作者在这里提出了两种方式，一种是 Metropolis-Hasting layer 和 Langevin layer，对应两种采样的方法。而后者正是 SDE-model 的方法。在这样的描述下，score-based model 就属于 stochastic normalizing flow 模型。只不过，langevin layer 是共享参数，只有输入 t 是不同的。作者在这里推导了一个损失函数的 ELBO 形式，我认为和 DDPM 的非常接近。实际上 DDPM 的描述也可以说是一种马尔可夫链。

最后作者提出，vae 实际是特殊的 stochastic normalizing flow 模型。vae 可以看成是一个 one-step 的马尔可夫链。状态只有两种，signal 和 encode。从这个角度推出的损失函数就是 vae 的损失函数。

由于这三种模型都是 stochastic normalizing flow 模型，所以可以对它们进行组合。

我认为这篇文章的视角比较独特，值得深入思考这些模型之间的关系。



