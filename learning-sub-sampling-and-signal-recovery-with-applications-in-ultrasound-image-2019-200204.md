### Paper:

Learning Sub-Sampling and Signal Recovery with Applications in Ultrasound Imaging

### Author:

Iris A.M. Huijben, Bastiaan S. Veeling, Kees Janse, Massimo Mischi, and Ruud J.G. van Sloun

### Year:

2019

### Notes:

阅读时间：2020.02.04

泛读。这篇文章研究的是超声图像。作者的思路是同时训练 sampling 和重建。sampling 通过类似分类器的概率来表示，并且设计了相关的求导的估计。另外在损失函数中，对于概率分布还增加了一个惩罚高 entropy 的损失函数。

网络结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-04%E4%B8%8B%E5%8D%888.24.12.png" width="70%"/>

概率表示：

<img src="http://latex.codecogs.com/svg.latex? r_{m} \sim \operatorname{Cat}\left(N, \boldsymbol{\pi}_{m}\right)" border="0"/>

$\pi_m$ 表示概率分布：

<img src="http://latex.codecogs.com/svg.latex? \pi_{m, n}=\frac{\exp \phi_{m, n}}{\sum_{n^{\prime}=1}^{N} \exp \phi_{m, n^{\prime}}}" border="0"/>

为了实现采样，作者增加了一个 gumble 变量：

<img src="http://latex.codecogs.com/svg.latex? \tilde{r}_{m}=\underset{n \in\{1, \ldots, N\}}{\operatorname{argmax}_{W}}\left\{\phi_{m, n}+e_{m, n}\right\}" border="0"/>

测量的mask 为：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \mathbf{a}_{m} &=\text { one }_{-} \operatorname{hot}_{N}\left\{\tilde{r}_{m}\right\}=\\ &=\text { one }_{-} \operatorname{hot}_{N}\left\{\underset{n \in\{1, \ldots, N\}}{\operatorname{argmax}, N\}}\left\{\phi_{m, n}+e_{m, n}\right\}\right\} \end{aligned}" border="0"/>

$\mathbf{a}_m$ 的梯度计算为：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\nabla_{\phi_{m}} \mathbf{a}_{m}:=} \\ {\nabla_{\phi_{m}} \mathbb{E}_{\mathbf{e}_{m}}\left[\operatorname{softmax}_{\tau}\left(\boldsymbol{\phi}_{m}+\mathbf{e}_{m}\right)\right]=} \\ {\nabla_{\phi_{m}} \mathbb{E}_{\mathbf{e}_{m}}\left[\frac{\exp \left\{\left(\boldsymbol{\phi}_{m}+\mathbf{e}_{m}\right) / \tau\right\}}{\sum_{n=1}^{N} \exp \left\{\left(\phi_{m, n}+e_{m, n}\right) / \tau\right\}}\right]}\end{array}" border="0"/>

损失函数的设计：

<img src="http://latex.codecogs.com/svg.latex? \mathcal{L}_{\mathcal{S}}=-\sum_{m=1}^{M} \sum_{n=1}^{N} \pi_{m, n} \log \pi_{m, n}" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \mathcal{L}_{m s e}=\mathbb{E}_{(\mathbf{x}, \mathbf{z}) \sim p_{\mathcal{D}}}\left[\left\|\mathbf{z}-g_{\theta}\left(\mathbf{A}_{\mathbf{\Phi}} \mathbf{x}\right)\right\|_{2}^{2}\right]" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \mathcal{L}_{p e n}=\lambda\|\theta\|_{2}+\mu \mathcal{L}_{\mathcal{S}}" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \hat{\theta}, \hat{\mathbf{\Phi}}=\underset{\theta, \mathbf{\Phi}}{\operatorname{argmin}}\left(\mathcal{L}_{m s e}+\mathcal{L}_{p e n}\right)" border="0"/>

### Bibtex:

```latex
@article{huijben2019learning,
  title={Learning Sub-Sampling and Signal Recovery with Applications in Ultrasound Imaging},
  author={Huijben, Iris AM and Veeling, Bastiaan S and Janse, Kees and Mischi, Massimo and van Sloun, Ruud JG},
  journal={arXiv preprint arXiv:1908.05764},
  year={2019}
}
```

