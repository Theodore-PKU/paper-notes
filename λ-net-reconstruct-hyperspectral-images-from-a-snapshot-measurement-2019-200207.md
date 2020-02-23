### Paper:

λ-net: Reconstruct Hyperspectral Images from a Snapshot Measurement

### Author:

Xin Miao, Xin Yuan, Yunchen Pu, Vassilis Athitsos

### Year:

2019

### Notes:

阅读时间：2020.02.07

泛读。这篇文章研究的是。hyperspectral image 的重建。文章首先介绍了求解问题的模型，然后介绍了自己的模型。这篇文章虽然使用的模型比较复杂，但是并没有很特别的思路。总体上说，作者的网络结构分成两个部分，第一个部分是用一个 conditional GAN 来生成多个 channel 的 hyperspectral image，第二个部分是用一个 U-net，单独对每个 channel 进行 refine。值得注意的是，GAN 的生成器和判别器部分都用了 self-attention 层，作者表示这样可以利用 long range 的信息，而不仅仅是局部的信息（这个层最早应该是在 style GAN 中使用的）。在训练方面，作者采用的策略是分步训练（主要原因是 GPU 内存的限制，一起训练两个网络也是可以的）

具体的笔记会依次介绍上述的各个内容。

hyperspectral image 的建模：不同波长对应一个 channel 的图像，测量的时候获得的是一个 2D 图像，每个 channel 的图像乘上一个 mask 之后累加得到测量值图像。

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{Y}=\sum_{k=1}^{B} \boldsymbol{X}_{k} \odot \boldsymbol{C}_{k}+\boldsymbol{G}" border="0"/>

如果写成线性系统，那么就是：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{y}=\boldsymbol{\Phi} \boldsymbol{x}+\boldsymbol{g}" border="0"/>

其中，$\mathbf{\Phi}=\left[\boldsymbol{D}_{1}, \ldots, \boldsymbol{D}_{B}\right], \boldsymbol{D}_{k}=\operatorname{diag}\left(\operatorname{vec}\left(\boldsymbol{C}_{k}\right)\right)$ 

GAN 部分，是一个 conditional GAN，输入是 $Y,\Phi$，判别器的输入也是这俩。作者没有提到判别器的结果。生成器是一个比较深的 U-Net。$Y$ 和 $\Phi$ 是通过 concatenation 的方式结合起来输入到网络中。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-07%E4%B8%8B%E5%8D%884.34.40.png" width=80%/>

在生成器部分，在 256 通道数那一层，作者还加了一个 self-attention layer。首先计算的是下面三个量：

<img src="http://latex.codecogs.com/svg.latex? f(\boldsymbol{\theta}) \in \mathbb{R}^{c^{\prime} \times h \times w}, g(\boldsymbol{\theta}) \in \mathbb{R}^{c^{\prime} \times h \times w}, h(\boldsymbol{\theta}) \in \mathbb{R}^{c \times h \times w}" border="0"/>

然后计算 attention map $A$，

<img src="http://latex.codecogs.com/svg.latex? a_{j, i}=\frac{\exp \left(s_{i j}\right)}{\sum_{i=1}^{N} \exp \left(s_{i j}\right)}, \text { with } s_{i j}=f^{\prime}\left(\theta_{i}\right)^{\top} g^{\prime}\left(\theta_{j}\right)" border="0"/>

$f^{\prime}(\theta_i), g^{\prime}(\theta_j)$ 分别表示两个不同位置的 $c^{\prime}$ channel 的feature，因为有 h x w 个位置，所以 $A\in \mathbb{R}^{N\times N}$, $N= w\times h$。得到 $A$ 之后，再通过下面两个运算得到最终的结果：

<img src="http://latex.codecogs.com/svg.latex?\boldsymbol{\xi}^{\prime}=\boldsymbol{A} h^{\prime}(\boldsymbol{\theta})^{\top} \in \mathbb{R}^{N \times c}" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{z}=\gamma \boldsymbol{\xi}+\boldsymbol{\theta}" border="0"/>

这里带 $\prime$ 记号的都是将 $h\times w$ 的 map 当作一个向量。示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-07%E4%B8%8B%E5%8D%884.36.54.png" width="70%"/>

在重建过程中，U-Net 并不是直接输出所有的 channel image，而是采用逐步生成的策略，先生成部分 channel，然后再生成所有的 channel。先生成的是 $\left[\boldsymbol{x}_{1}, \boldsymbol{x}_{5}, \boldsymbol{x}_{9}, \boldsymbol{x}_{13}, \boldsymbol{x}_{17}, \boldsymbol{x}_{21}\right]$，然后是 $\left[\boldsymbol{x}_{1}, \boldsymbol{x}_{3}, \boldsymbol{x}_{5}, \dots, \boldsymbol{x}_{23}\right]$，最后是所有的 24 个 channel。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-07%E4%B8%8B%E5%8D%884.46.56.png" width="70%"/>

GAN 部分的损失函数有两个，一个是对抗loss，一个是生成的中间结果的 2 范数loss。

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \mathcal{L}_{\ell_{2}}(G)=& \mathbb{E}_{\boldsymbol{y}, \boldsymbol{x}}\left[\left\|\boldsymbol{x}^{1}-I_{1}(\boldsymbol{y}, \boldsymbol{\Phi})\right\|_{2}+\left\|\boldsymbol{x}^{2}-I_{2}(\boldsymbol{y}, \boldsymbol{\Phi})\right\|_{2}\right.\\ &\left.+\|\boldsymbol{x}-G(\boldsymbol{y}, \mathbf{\Phi})\|_{2}\right] \end{aligned}" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \left(G^{*}, D^{*}\right)=\arg \min _{G} \max _{D} \mathcal{L}_{\mathrm{GAN}}(G, D)+\alpha \mathcal{L}_{\ell_{2}}(G)" border="0"/>

得到初步的重建结果之后，需要一个较小型的 U-net 进行 refine。在训练时，先训练 GAN，然后固定参数，训练 refine U-net。不一起训练的原因是作者希望在 refine 的阶段可以用不同的 scene。原来重建部分的 GAN 用的是一个 scene。这样做是因为 GPU 的限制。

### Bibtex:

```latex
@inproceedings{miao2019lambda,
  title={$\lambda$-net: Reconstruct Hyperspectral Images from a Snapshot Measurement},
  author={Miao, Xin and Yuan, Xin and Pu, Yunchen and Athitsos, Vassilis},
  booktitle={IEEE/CVF Conference on Computer Vision (ICCV)},
  volume={1},
  year={2019}
}
```

