### Paper:

Plug-and-Play Methods Provably Converge with Properly Trained Denoisers

### Author:

Ernest K. Ryu, Jialin Liu, Sicheng Wang, Xiaohan Chen, Zhangyang Wang, Wotao Yin

### Year:

2019

### Notes:

阅读日期：2020.02.15

泛读。这篇文章研究的是 plug and play 的方法的收敛性。plug-and-play 的方法值得是一类将传统算法中的某些计算换成其他模型（比如神经网络），主要替换的是 proximal 算子，典型的有 ISTA，ADMM 等算法。作者在这篇文章中提出了一个关于其他模型的收敛条件，和李普西次条件有关。并且为了达到这个条件，设计了一个网络训练时的一个技巧（spectral normalization）。

设目标函数为：

<img src="http://latex.codecogs.com/svg.latex? \underset{x \in \mathbb{R}^{d}}{\operatorname{minimize}} f(x)+\gamma g(x)" border="0"/>

如果是 proximal gradient 算法，迭代为：

<img src="http://latex.codecogs.com/svg.latex? x^{k+1}=H_{\sigma}(I-\alpha \nabla f)\left(x^{k}\right)" border="0"/>

注意，这里的 $H_{\sigma}$ 指的都是替换 proximal 算子之后的函数。

ADMM 算法变成：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} x^{k+1} &=H_{\sigma}\left(y^{k}-u^{k}\right) \\ y^{k+1} &=\operatorname{Prox}_{\alpha f}\left(x^{k+1}+u^{k}\right) \\ u^{k+1} &=u^{k}+x^{k+1}-y^{k+1} \end{aligned}" border="0"/>

为了便于分析，作者给出了一个和 ADMM 等家的 DRS 算法：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} x^{k+1 / 2} &=\operatorname{Prox}_{\alpha f}\left(z^{k}\right) \\ x^{k+1} &=H_{\sigma}\left(2 x^{k+1 / 2}-z^{k}\right) \\ z^{k+1} &=z^{k}+x^{k+1}-x^{k+1 / 2} \end{aligned}" border="0"/>

作者提出的 $H_{\sigma}$ 要满足的条件是：

<img src="http://latex.codecogs.com/svg.latex? \left\|\left(H_{\sigma}-I\right)(x)-\left(H_{\sigma}-I\right)(y)\right\|^{2} \leq \varepsilon^{2}\|x-y\|^{2}" border="0"/>

假设该条件满足，作者可以证明 proximal gradient 和 ADMM 算法对应的不动点问题有很好的李普西次条件。

作者提出的 spectral normalization 适用于卷积神经网络，因为大部分的 $H_{\sigma}$ 都是一个去噪网络。具体的计算如下：

1. 对于每个卷积 $\mathcal{K}_l$，初始都有一个符合输入输出维度的张量 $U_l, V_l$。 	

2. 更新 $U_l, V_l$, 

   <img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{V_{l} \leftarrow \mathcal{K}_{l}^{*}\left(U_{l}\right) /\left\|\mathcal{K}_{l}^{*}\left(U_{l}\right)\right\|_{2}} \\ {U_{l} \leftarrow \mathcal{K}_{l}\left(V_{l}\right) /\left\|\mathcal{K}_{l}\left(V_{l}\right)\right\|_{2}}\end{array}" border="0"/>

3. 更新 $\mathcal{K}_l$

   <img src="http://latex.codecogs.com/svg.latex? K_{l} \leftarrow K_{l} / \sigma\left(\mathcal{K}_{l}\right), \text { where } \sigma\left(\mathcal{K}_{l}\right)=\left\langle U_{l}, \mathcal{K}_{l}\left(V_{l}\right)\right\rangle" border="0"/>

### Bibtex:

```latex
@article{ryu2019plug,
  title={Plug-and-play methods provably converge with properly trained denoisers},
  author={Ryu, Ernest K and Liu, Jialin and Wang, Sicheng and Chen, Xiaohan and Wang, Zhangyang and Yin, Wotao},
  journal={arXiv preprint arXiv:1905.05406},
  year={2019}
}
```

