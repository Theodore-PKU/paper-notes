### Paper:

NETT Regularization for Compressed Sensing Photoacoustic Tomography

### Author:

Stephan Antholzer and Johannens Schwab

### Year:

2019

### Notes:

阅读时间：2020.02.04

泛读。这篇文章研究的是 PAT 重建，这个医学成像有点类似 CT，还是一个压缩感知问题。作者的想法是用训练的神经网络作为图像的正则项，其表示形式为 $\|\mathcal{V}_{\theta}(x)\|^2$，网络的输出是残差，如果 $x$ 是真实图像，则 target 为 0。在训练好神经网络之后，作者直接极小化目标函数（即 data fidelity 和 正则项），使用的是一阶梯度的方法，连正则项部分也是，对输入 $x$ 求导，而不是直接用 $\mathcal{V}_{\theta}$ 来替代。

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\hat{\mathbf{f}}^{k+1}=\mathbf{f}^{k}-\mu\left(\mathcal{A}^{T}\left(\mathcal{A} \mathbf{f}^{k}-\mathbf{g}\right)\right)} \\ {\mathbf{f}^{k+1}=\hat{\mathbf{f}}^{k}-\mu \lambda\left(\nabla_{\mathbf{f}} \mathcal{V}_{\theta}\left(\mathbf{f}^{k}\right)\right)}\end{array}" border="0"/>

training 使用的样本是：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{ll}{\mathbf{x}_{k}=\mathbf{b}_{k},} & {\mathbf{y}_{k}=\mathbf{b}_{k}-\mathbf{f}_{k} \quad \text { for } k=1, \ldots, N_{1}} \\ {\mathbf{x}_{k}=\mathbf{f}_{k},} & {\mathbf{y}_{k}=\mathbf{0}}\end{array} \quad \begin{array}{ll}{\text { for } k=1, \ldots, N_{1}} \\ {\text { for } k=N_{1}+1, \ldots, N_{1}+N_{2}}\end{array}" border="0"/>

作者用的网络非常简单：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-04%E4%B8%8B%E5%8D%886.56.10.png"/>

从实验结果看，比较奇怪，网络这么简单，效果还比 U-Net 好，猜测和训练样本量有关。因此在有的情况下还比不上一般的 $l_1$ 正则项的方法。

### Bibtex:

```latex
@inproceedings{antholzer2019nett,
  title={NETT regularization for compressed sensing photoacoustic tomography},
  author={Antholzer, Stephan and Schwab, Johannes and Bauer-Marschallinger, Johnnes and Burgholzer, Peter and Haltmeier, Markus},
  booktitle={Photons Plus Ultrasound: Imaging and Sensing 2019},
  volume={10878},
  pages={108783B},
  year={2019},
  organization={International Society for Optics and Photonics}
}
```

