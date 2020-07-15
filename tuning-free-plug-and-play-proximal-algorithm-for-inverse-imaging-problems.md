# Tuning-free Plug-and-Play Proximal Algorithm for Inverse Imaging Problems

Author: Kaixuan Wei, Angelica Aviles-Rivero, Jingwei Liang, Ying Fu, Carola-Bibiane Schnlieb, Hua Huang

Year: 2020

Published in: ICML

```latex
@article{wei2020tuning,
  title={Tuning-free Plug-and-Play Proximal Algorithm for Inverse Imaging Problems},
  author={Wei, Kaixuan and Aviles-Rivero, Angelica and Liang, Jingwei and Fu, Ying and Schnlieb, Carola-Bibiane and Huang, Hua},
  journal={arXiv preprint arXiv:2002.09611},
  year={2020}
}
```

Link: https://github.com/Theodore-PKU/paper-notes/blob/master/tuning-free-plug-and-play-proximal-algorithm-for-inverse-imaging-problems.md

Note: /Users/xieyutong/Documents/Research/PaperReading/Notes/tuning-free-plug-and-play-proximal-algorithm-for-inverse-imaging-problems.md



这篇文章的主要想法是利用强化学习来实现对 PnP 算法的自动调参。PnP 算法就是讲 ADMM 这类处理逆问题的算法中的 proximal 算子用一个 denoiser 表示，denoiser 可以是 BM3D 也可以是神经网络。算法框架也可以不是 ADMM。和 unrolling 形式的网络类似，但是有一些区别，比如后者的迭代次数是固定的，网络的训练不是 off-the-shelf。

PnP 算法中的参数对结果影响比较大，这里的参数主要是：去噪时的噪声参数，罚项系数和停时。前两个是连续变量，停时是离散变量。这三个对应的就是 action。

PnP 算法的框架大概是：

目标函数：
$$
\operatorname{minimize}_{x \in \mathbb{R}^{N}} \mathcal{D}(x)+\lambda \mathcal{R}(x)
$$
核心proximal 算子：
$$
\operatorname{Prox}_{\sigma^{2} \mathcal{R}}(v)=\underset{x}{\operatorname{argmin}}\left(\mathcal{R}(x)+\frac{1}{2 \sigma^{2}}\|x-v\|_{2}^{2}\right)
$$
PnP-ADMM
$$
\begin{array}{l}
x_{k+1}=\operatorname{Prox}_{\sigma_{k}^{2} \mathcal{R}}\left(z_{k}-u_{k}\right)=\mathcal{H}_{\sigma_{k}}\left(z_{k}-u_{k}\right) \\
z_{k+1}=\operatorname{Prox}_{\frac{1}{\mu_{k}}} \mathcal{D}\left(x_{k+1}+u_{k}\right) \\
u_{k+1}=u_{k}+x_{k+1}-z_{k+1}
\end{array}
$$
涉及到强化学习，所以不过多讨论具体的算法细节。首先状态就是 PnP 算法中的 x 等变量，reward 是训练时的中间值的 PNSR，另外 reward 对停时有一个惩罚（$\eta$）：
$$
r\left(s_{t}, a_{t}\right)=\zeta\left(p\left(s_{t}, a_{t}\right)\right)-\zeta\left(s_{t}\right)-\eta
$$
作者把强化学习部分分为两个子 policy 网络，一个网络输出是噪声参数、罚项系数，一个网络的输出是停时的概率。分别记为 $a_2, a_1$，所以整体的网络图为：

![屏幕快照 2020-07-15 下午9.53.12](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-07-15 下午9.53.12.png)

policy 网络的设计大概是一个卷积网络，两个子网络是浅层共享参数，之后用两组全连接层输出 $a_1, a_2$。

Denoiser 的网络有好几种选择，其中一个是 residual U-net。

实验有两个，其中一个是 MRI。