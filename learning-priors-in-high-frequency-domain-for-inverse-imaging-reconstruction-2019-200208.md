### Paper:

Learning Priors in High-frequency Domain for Inverse Imaging Reconstruction

### Author:

Zhuonan He, Jinjie Zhou, Dong Liang, Senior, Yuhao Wang, Qiegen Liu

### Year:

2019

### Notes:

阅读日期：2020.02.08

泛读。这篇文章研究的是图像逆问题的重建，作者在论文中的实验例子是 MRI 和 low-dose CT 的重建。整体的思路是用自编码网络训练出一个去噪的 prior，然后把这个 prior 放入原来的带 data fidelity 的目标函数中，最后用proxmal graident 计算。这篇文章创新的地方主要是是自编码网络的输入不是带噪声图像，而是先把图像分解成高频和低频部分，将具有噪声的高频部分输入到网络中进行去噪，最后再和低频部分加起来。在分解成高频和低频部分时，作者使用了不止一个低频分解，也就是说，用了不同程度的高频低频分解，合成的时候取平均作为最终的输出。作者用的高低频分解方法是梯度的 2 范数作为惩罚项，而系数作为控制高低频分解的变量。自编码用的是 RED-NET。除了高低频分解，没有什么特别的地方。作者在论文中表示，这种做法是受字典学习和卷积稀疏编码的启发（但我感觉没啥关系），以及作者分析了分解和合成两个步骤，这部分我认为有很大的错误，并且这些分析也对后文实际的分解和合成做法没有什么关联。

自编码网络是事先训练好的。

**Prior 的设计**

高低频的分解：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{u_{l}=\arg \min _{u_{l}}\left\|u_{l}-u\right\|_{2}^{2}+\alpha\left\|\nabla u_{l}\right\|_{2}^{2}=\left(I+\alpha \nabla^{T} \nabla\right)^{-1} u} \\ {u_{h}=u-u_{l}}\end{array}" border="0"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200208162630.png"/>

**整体算法**

最终的目标函数（这里的 $W$ 表示的都是分解的算子）：

<img src="http://latex.codecogs.com/svg.latex? \min _{u} F(H u, y)+\lambda\left\|W(u)-A_{\sigma_{\eta}}(W(u))\right\|^{2}" border="0"/>

这个式子可以用下式替代（可能是因为 proximal gradient？）：

<img src="http://latex.codecogs.com/svg.latex? \min _{u} F(H u, y)+\frac{\lambda}{\beta}\left\|u-\left(u^{k}-\beta \nabla G\left(u^{k}\right)\right)\right\|^{2}" border="0"/>

其中：

<img src="http://latex.codecogs.com/svg.latex? \nabla G(u)=W^{-1}\left\{\left[1-\nabla A_{\sigma_{\eta}}^{T}(W(u))\right]\left[W(u)-A_{\sigma_{\eta}}(W(u))\right]\right\}" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? G(u)=\left\|W(u)-A_{\sigma_{\eta}}(W(u))\right\|^{2}" border="0"/>

迭代求解的时候包含梯度计算（MRI 例子）和 LS 的解：

<img src="http://latex.codecogs.com/svg.latex? u^{k+1}=\frac{H^{T} y+\lambda\left[u^{k}-\nabla G\left(u^{k}\right)\right]}{\left(H^{T} H+\lambda\right)}" border="0"/>

### Bibtex:

```latex
@article{he2019learning,
  title={Learning Priors in High-frequency Domain for Inverse Imaging Reconstruction},
  author={He, Zhuonan and Zhou, Jinjie and Liang, Dong and Wang, Yuhao and Liu, Qiegen},
  journal={arXiv preprint arXiv:1910.11148},
  year={2019}
}
```

