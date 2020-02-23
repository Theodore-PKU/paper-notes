### Paper:

Douglas-Rachford Networks: Learning Both the Image Prior and Data Fidelity Terms for Blind Image Deconvolution

### Author:

Raied Aljadaany Dipan K. Pal Marios Savvides

### Year:

2019

### Notes:

阅读日期：2020.02.17

泛读。这篇文章研究的是盲去卷积的问题。盲去卷积和去卷积的差别在于卷积核是未知的。作者使用的方法是展开  DR 算法，DR 算法和 ADMM 算法本质是相同的。因为卷积核未知，所以 data fidelity 项对应的 proximal 算子也用神经网络表示，图像的 prior 当然也还是神经网络表示。另外，作者在论文中说，因为是一个非凸问题，所以需要一个好的初始值，所以有一个预处理网络，但是这个预处理网络的输出不是图像了，而是张量，通道数是 N = 256，这样一来，DR 网络部分其实不是处理图像，而且，最后还需要一个后处理网络还原回原始的图像 channel 数。损失函数是 2 范数 + GAN-loss。如果没有 GAN-loss，我感觉完全没有提升效果。实验的时候，卷积核 k 都是随机选择的。也就是说，作者的这个盲卷积是完全随机的。个人认为这种做法意义不大，也无法用 DR 算法展开来解释。因为缺乏对卷积核的估计，而不同的卷积核，显然 proximal 算子也是不同的，这种展开没有其实际意义，更像是一种网络结构设计，变成炼丹了。

盲去卷积的任务：

<img src="http://latex.codecogs.com/svg.latex? x^{*}=\arg \min _{x}\|y-k * x\|_{2}^{2}+g(x)" border="0"/>

DR 算法：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} q^{k} &=\operatorname{prox}_{f, \beta}\left(x^{k}\right) \\ z^{k} &=\operatorname{prox}_{g, \beta}\left(2 q^{k}-x^{k}\right) \\ x^{k+1} &=x^{k}+\lambda_{k}\left(z^{k}-q^{k}\right) \end{aligned}" border="0"/>

DR-net：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \min _{\Theta, \lambda} L\left(x_{g t}, x^{S}\right) \text { s.t } & q^{k}=\Gamma_{f, \theta}^{k}\left(x^{k}\right) \\ & z^{k}=\Gamma_{g, \theta}^{k}\left(2 q^{k}-x^{k}\right) \\ & x^{k+1}=x^{k}+\lambda_{k}\left(z^{k}-q^{k}\right) \end{aligned}" border="0"/>

这里的 loss 没有写包含 GAN-loss，实际上最后是有对抗loss的。

完整的网络结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200217210716.png"/>

DR block 的两个 proximal 算子的结构和整体的连接：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200217210823.png"/>

### Bibtex:

```latex
@inproceedings{aljadaany2019douglas,
  title={Douglas-rachford networks: Learning both the image prior and data fidelity terms for blind image deconvolution},
  author={Aljadaany, Raied and Pal, Dipan K and Savvides, Marios},
  booktitle={Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition},
  pages={10235--10244},
  year={2019}
}
```

