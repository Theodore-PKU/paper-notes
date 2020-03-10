### Paper:

Model-based Convolutional De-Aliasing Network Learning for Parallel MR Imaging

### Author:

Yanxia Chen, Taohui Xiao, Cheng Li, Qiegen Liu, and Shanshan Wang

### Year:

2019

### Notes:

阅读日期：2020.02.21

泛读。这篇文章研究 parallel MRI 重建，使用的方法是 unroll ADMM 算法，整体框架和 ADMM-net 非常相似，不过处理的是 multi-coil（虽然这部分我感觉论文写的很不清楚，没有明白具体是怎么做的）。总的来说还是把 ADMM 算法改写成网络形式，网络的部分出现在原始目标函数中的变换，这个子问题用的是迭代算法。主要的疑惑的地方是，作者表示不考虑 sensitive map，在目标函数中也没有出现，在网络的设计上也没有看出 sensitive map 以何种方式进行处理，个人认为这样做非常不合理。

<img src="http://latex.codecogs.com/svg.latex? \underset{\mathbf{X}}{\arg \min }\left\{\frac{1}{2}\|\mathbf{A} \mathbf{X}-\mathbf{Y}\|_{2}^{2}+\lambda_{s} \mathcal{P}\left(\mathbf{\Phi}_{\mathbf{s}} \mathbf{X}\right)+\lambda_{coils} \mathcal{P}\left(\mathbf{\Phi}_{\mathrm{coils}} \mathbf{X}\right)\right\}" border="0"/>

这是作者给出的目标函数，作者表示两个变换 $\Phi$ 分别是图像域和kspace域，但是 $X$ 却是同一个，这里就很有问题。

构建为 ADMM 算法后的目标函数和迭代式子为：

<img src="http://latex.codecogs.com/svg.latex? \underset{\mathbf{X}, \mathbf{V}}{\arg \min }\left\{\frac{1}{2}\|\mathbf{A} \mathbf{X}-\mathbf{Y}\|_{2}^{2}+\sum_{l=1}^{L} \lambda_{l} \mathcal{P}\left(\mathbf{\Phi}_{l} \mathbf{V}\right)\right\} \quad \text { s.t. } \mathbf{X}=\mathbf{V}" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{\mathbf{X}^{k+1}=\min _{\mathbf{X}} \frac{1}{2}\|\mathbf{A} \mathbf{X}-\mathbf{Y}\|_{2}^{2}+\frac{\rho}{2}\left\|\mathbf{X}-\mathbf{V}^{k}-b^{k}\right\|_{2}^{2}} \\ {\mathbf{V}^{k+1}=\min _{\mathbf{V}} \sum_{l=1}^{L} \lambda_{l} \mathcal{P}\left(\mathbf{\Phi}_{l} \mathbf{V}\right)+\frac{\rho}{2}\left\|\mathbf{X}^{k+1}-\mathbf{V}-b^{k}\right\|_{2}^{2}} \\ {b^{k+1}=b^{k}+\mathbf{V}^{k+1}-\mathbf{X}^{k+1}}\end{array}\right." border="0"/>

最终展开式为：

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{\mathbf{X}^{(n)}=\mathbf{F}^{T}\left(\mathbf{M}^{T} \mathbf{M}+\rho^{(n)} \mathbf{I}\right)^{-1}\left[\mathbf{M}^{T} \mathbf{Y}+\rho^{(n)} \mathbf{F}\left(\mathbf{V}^{n-1}-b^{n-1}\right)\right]} \\ {\mathbf{V}^{(n, k)}=\mu_{1} \mathbf{V}^{(n, k-1)}+\mu_{2}\left(\mathbf{X}^{(n)}+b^{(n-1)}\right)-\sum_{l=1}^{L} \tilde{\lambda}_{l} \mathbf{\Phi}_{l}^{T} \mathcal{F}\left(\mathbf{\Phi}_{l} \mathbf{V}^{(n, k-1)}\right)} \\ {b^{k+1}=b^{k}+\tilde{\eta}\left(\mathbf{X}^{(n)}-\mathbf{V}^{(n)}\right) \quad \forall k \geq 1}\end{array}\right." border="0"/>

网络展开的主要内容是第二步：

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{\text {Recon }: \mathbf{X}^{(n)}=\mathbf{F}^{T}\left(\mathbf{M}^{T} \mathbf{M}+\rho^{(n)} \mathbf{I}\right)^{-1}\left[\mathbf{M}^{T} \mathbf{Y}+\rho^{(n)} \mathbf{F}\left(\mathbf{V}^{(n-1)}-b^{(n-1)}\right)\right]} \\ {\text {Addition }: \mathbf{V}^{(n, k)}=\mu_{1} \mathbf{V}^{(n, k-1)}+\mu_{2}\left(\mathbf{X}^{(n)}+b^{(n-1)}\right)-\mathbf{C}_{2}^{(n, k)}} \\ {\text {Conv1}: \mathbf{C}_{1}^{(n, k)}=\sum_{l=1}^{L}\left(\boldsymbol{w}_{1, l}^{(n, k)} \times \mathbf{V}^{(n, k-1)}+\boldsymbol{b}_{1, l}^{(n, k)}\right)} \\ {\text {Nonlinear}: \boldsymbol{h}^{(n, k)}=\boldsymbol{S}_{P L F}\left(\mathbf{C}_{1}^{(n, k)} ;\left\{\boldsymbol{p}_{i}, \boldsymbol{q}_{i}^{(n, k)}\right)\right.} \\ {\text{Conv2}: \mathbf{C}_{2}^{(n, k)}=\sum_{l=1}^{L}\left(\boldsymbol{w}_{2, l}^{(n, k)} \times \boldsymbol{h}^{(n, k)}+\boldsymbol{b}_{2, l}^{(n, k)}\right)} \\ \text{Multi}: b^{(n)}=b^{(n-1)}+\tilde{\eta}\left(\mathbf{X}^{(n)}-\mathbf{V}^{(n)}\right)\end{array}\right." border="0"/>

注意到，网络替代的是 $\Phi_l,\Phi^T_l$，$\mathcal{F}$ 替换为 $S_{PLE}$ 函数，但是，就是这里的网络结构不懂具体的含义：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200221133604.png"/>

### Bibtex:

```latex
@inproceedings{chen2019model,
  title={Model-based Convolutional De-Aliasing Network Learning for Parallel MR Imaging},
  author={Chen, Yanxia and Xiao, Taohui and Li, Cheng and Liu, Qiegen and Wang, Shanshan},
  booktitle={International Conference on Medical Image Computing and Computer-Assisted Intervention},
  pages={30--38},
  year={2019},
  organization={Springer}
}
```

