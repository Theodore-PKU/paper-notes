### Paper:

Cardiac MR Segmentation from Undersampled k-space Using Deep Latent Representation Learning

### Author:

Jo Schlemper, Ozan Oktay, Wenjia Bai, Daniel C. Castro, Jinming Duan, Chen Qin, Jo V. Hajnal, and Daniel Rueckert

### Year:

2018

### Notes:

阅读日期：2020.02.29

泛读。这篇文章研究的是 undersample 情况下的 MRI 分割问题。作者认为当我们需要的 clinical parameters 是比原始图像压缩性更强的时候，需要的测量值应该更少。不过这篇文章完全没有涉及到重建的问题，即便如此，还是有一些启发意义，主要在于 AE 的 coding 和空间划分之间的关系，以及 latent space 的研究对于空间划分应该是很重要的。作者提出的分割方法有两种，一种是直接用 U-net 输出分割结果，损失函数是 cross entropy，这个网络称为 Syn-net，网络结构就是 U-net。另一种方法则复杂一下，称为 LI-net。首先，作者用 AE 训练分割 label，这样可以得到分割结果的一个 coding 表示。然后训练一个 predict network（此时 AE 的参数固定），输入是 image，生成对应分割结果的 AE 中的 coding，最后用 AE 的解码器对 coding 解码，由此得到 image 的分割结果。LI-net 方法的损失函数比较复杂，既要让图像（不论是 full-sample 还是 undersample）的预测coding和 AE 的一致，也要让 full-sample 和 undersample 的coding结果一致，当然还有最后的分割结果的一致。实验表明，当测量值较多时，Syn-net 表现比 LI-net 好，当测量值较少时 LI-net 比 Syn-net 好。

Syn-net 损失函数是：

<img src="http://latex.codecogs.com/svg.latex? \mathcal{L}(\theta)=\sum_{(x, y) \in \mathcal{D}} \mathbb{E}_{u \sim r}\left[\sum_{i \in S} p\left(y_{i} | x_{i}\right) \log q\left(y_{i} | u_{i}, \theta\right)\right]">

LI-net 的 stage 2 损失函数：

<img src="http://latex.codecogs.com/svg.latex? \mathcal{L}\left(\theta_{\text {pred}}\right)=\sum_{(x, y) \in \mathcal{D}} \mathbb{E}_{u \sim r}\left[d_{\mathcal{H}}(y, u)+\lambda_{1} d_{\mathcal{H}}(y, x) +\lambda_{2}\|\Pi(x)-\Pi(u)\|_{2}+\lambda_{3} d_{\mathrm{CE}}(y, \Psi \circ \Pi(u))\right]">

<img src="http://latex.codecogs.com/svg.latex? d_{\mathcal{H}}(y, x)=\left\|\Phi\left(y ; \theta_{e n c}\right)-\Pi\left(x ; \theta_{p r e d}\right)\right\|_{2}">

AE 的损失函数就很简答，可以是欧式距离，也可以是 cross entropy。

结构示意图

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200301204544.png"/>

### Bibtex:

```latex
@inproceedings{schlemper2018cardiac,
  title={Cardiac MR segmentation from undersampled k-space using deep latent representation learning},
  author={Schlemper, Jo and Oktay, Ozan and Bai, Wenjia and Castro, Daniel C and Duan, Jinming and Qin, Chen and Hajnal, Jo V and Rueckert, Daniel},
  booktitle={International Conference on Medical Image Computing and Computer-Assisted Intervention},
  pages={259--267},
  year={2018},
  organization={Springer}
}
```

### Related work

none

### Code or link

none