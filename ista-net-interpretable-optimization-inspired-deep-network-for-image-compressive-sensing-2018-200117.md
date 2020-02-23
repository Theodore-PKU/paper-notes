### Paper:

ISTA-Net: Interpretable Optimization-Inspired Deep Network for Image Compressive Sensing

### Author:

Jian Zhang, Bernard Ghanem

### Year:

2018

### Notes:

阅读日期：2020.01.17

精读。这篇文章研究的是压缩感知的重建问题。作者的思路是对 ISTA 算法进行展开，不过和之前 lecun 的 LISTA 算法不同，这篇文章在具体的处理上不一样。ISTA 算法（我感觉和 proximal gradient 算法几乎一样的？？），分为两步。作者提出的方法主要是对第二步进行网络的构建。作者不是单纯地用神经网络来替代 proximal 算子，而是针对proximal算子的目标函数，通过数学的推导近似出另一种形式的计算过程，然后再用网络来学习这些非线性函数。另外，在此基础上，作者将残差学习考虑进来，得到改进版本的 ISTA-Net+。实验证明改进后的算法效果更好，且收敛更快。从实验效果上看，还是非常优异的。

ISTA 算法：（第一步）

<img src="http://latex.codecogs.com/svg.latex? \mathbf{r}^{(k)}=\mathbf{x}^{(k-1)}-\rho \mathbf{\Phi}^{\top}\left(\mathbf{\Phi} \mathbf{x}^{(k-1)}-\mathbf{y}\right)" border="0"/>

这一步不需要网络来构建。

第二步：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{x}^{(k)}=\underset{\mathbf{x}}{\arg \min } \frac{1}{2}\left\|\mathbf{x}-\mathbf{r}^{(k)}\right\|_{2}^{2}+\lambda\|\mathbf{\Psi} \mathbf{x}\|_{1}" border="0"/>

更一般化的写法为：

<img src="http://latex.codecogs.com/svg.latex? \operatorname{prox}_{\lambda \phi}(\mathbf{r})=\arg \min _{\mathbf{x}} \frac{1}{2}\|\mathbf{x}-\mathbf{r}\|_{2}^{2}+\lambda \phi(\mathbf{x})" border="0"/>

对于传统的算法，计算结果应该为：

<img src="http://latex.codecogs.com/svg.latex? \operatorname{prox}_{\lambda \phi}(\mathbf{r})=\mathbf{W}^{\top} \operatorname{soft}(\mathbf{W r}, \lambda)" border="0"/>

下面推导出作者的网络结构对应的数学计算过程。设 $\Psi$ 用 $\mathcal{F}(\cdot)$ 替代，如果 $\mathcal{F}(\cdot)$ 是一个浅层网络，可以有如下近似：

<img src="http://latex.codecogs.com/svg.latex? \left\|\mathcal{F}(\mathbf{x})-\mathcal{F}\left(\mathbf{r}^{(k)}\right)\right\|_{2}^{2} \approx \alpha\left\|\mathbf{x}-\mathbf{r}^{(k)}\right\|_{2}^{2}" border="0"/>

那么proximal 的目标函数就可以替换为：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{x}^{(k)}=\underset{\mathbf{x}}{\arg \min } \frac{1}{2}\left\|\mathcal{F}(\mathbf{x})-\mathcal{F}\left(\mathbf{r}^{(k)}\right)\right\|_{2}^{2}+\theta\|\mathcal{F}(\mathbf{x})\|_{1}" border="0"/>

对应的闭式解为：

<img src="http://latex.codecogs.com/svg.latex? \mathcal{F}\left(\mathbf{x}^{(k)}\right)=\operatorname{soft}\left(\mathcal{F}\left(\mathbf{r}^{(k)}\right), \theta\right)" border="0"/>

只要对 $\mathcal{F}(\mathbf{x}^{(k)})$ 求逆即可，因此有（$\theta$ 是 soft() 函数的阈值参数，每一次迭代都可变）：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{x}^{(k)}=\widetilde{\mathcal{F}}\left(\operatorname{soft}\left(\mathcal{F}\left(\mathbf{r}^{(k)}\right), \theta\right)\right)" border="0"/>

对于 $\mathcal{F}(\mathbf{x}^{(k)})$ 及其逆，作者都用一个两层的网络替代。（这里和示意图有一点出入的感觉）

ISTA-Net 示意图

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-17%E4%B8%8B%E5%8D%887.24.57.png"/>

损失函数的设计为 $\mathcal{F}$ 及其逆运算的约束和图像生成的损失函数：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-17%E4%B8%8B%E5%8D%887.37.57.png" width="60%"/>

ISTA-Net+ 的改进部分主要在于加入了残差的考虑，因此第二步的数学计算过程有一些改变。具体来说，如果设：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{x}^{(k)}=\mathbf{r}^{(k)}+\mathbf{w}^{(k)}+\mathbf{e}^{(k)}" border="0"/>

用 $\mathcal{R}(x)$ 表示 $w$，这里作者设 $\mathcal{R}(x) = G(D(x))$，（我觉得这里太过灵活了，没有那么好的数学模型对应），目标函数变成（令 $\mathcal{F} = \mathcal{H}(\mathcal{D}(x))$ ）：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{x}^{(k)} = \min _{\mathbf{x}} \frac{1}{2}\left\|\mathcal{H}(\mathcal{D}(\mathbf{x}))-\mathcal{H}\left(\mathcal{D}\left(\mathbf{r}^{(k)}\right)\right)\right\|_{2}^{2}+\theta\|\mathcal{H}(\mathcal{D}(\mathbf{x}))\|_{1}" border="0"/>

于是网络结构就变成了：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{x}^{(k)}=\mathbf{r}^{(k)}+\mathcal{G}\left(\widetilde{\mathcal{H}}\left(\operatorname{soft}\left(\mathcal{H}\left(\mathcal{D}\left(\mathbf{r}^{(k)}\right)\right), \theta\right)\right)\right)" border="0"/>

示意图为：

<img src="https://cdn.mathpix.com/snip/images/Km2APcpWM2Bz1_od_CJOag436zbfijNqKLRt6Qk1L0Y.original.fullsize.png" />

作者认为 ISTA-Net+ 的有效性体现在两点：残差的稀疏性；残差学习的网络有效性。

### Bibtex:

```latex
@inproceedings{zhang2018ista,
  title={ISTA-Net: Interpretable optimization-inspired deep network for image compressive sensing},
  author={Zhang, Jian and Ghanem, Bernard},
  booktitle={Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition},
  pages={1828--1837},
  year={2018}
}
```

