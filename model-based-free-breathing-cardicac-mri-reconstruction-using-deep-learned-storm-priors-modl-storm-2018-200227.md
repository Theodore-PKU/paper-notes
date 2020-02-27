### Paper:

Model-based Free-Breathing Cardiac MRI Reconstruction Using Deep Learned & Storm Priors: MODL-STORM

### Author:

Sampurna Biswas, Hemant K. Aggarwal, Sunrita Poddar, and Mathews Jacob

### Year:

2018

### Notes:

阅读日期：2020.02.27

泛读。这篇文章研究的是动态的心血管 MRI 重建。使用的方法是 unroll 形式的 network，因为是动态 MRI，所以作者的目标函数除了 CNN 的 prior，还有一个 frame 之间的 prior。也只有利用 unroll 形式的 network，才可以很容易的包含其他 prior。作者认为直接用 CNN 作为重建算子在这个问题上是不合适的。其他的内容和一般的 unroll network 的方法差不多。有一个需要指出的是，作者给出的训练方法中，对于其中一个需要大量计算的中间值，选择每次更新参数前，先算好，然后固定，以此来计算其他参数的梯度，否则内存无法存下这么多变量。在更新完其他参数后，再把这个中间单独算出来。CNN 的结构非常简单，就是一个多层3D CNN，因为不可能同时输入所有的 frame，所以也需要对 batch 的大小比较小心。总的来说比较一般。

原始目标函数：

<img src="http://latex.codecogs.com/svg.latex? \mathcal{C}(\mathbf{X})=\underbrace{\|\mathcal{A}(\mathbf{X})-\mathbf{B}\|_{2}^{2}}_{\text {data consistency }}+\lambda_{1} \underbrace{\left\|\mathcal{N}_{\mathbf{w}}(\mathbf{X})\right\|^{2}}_{\text {CNN prior }} +\underbrace{\lambda_{2} \operatorname{tr}\left(\mathbf{X}^{T} \mathbf{L X}\right)}_{\text {STORM prior }}" border="0"/>

$\mathbf{L}=\mathbf{D}-\mathbf{W}$，这是事先计算好的，$\mathbf{D}_{(i, i)}=\sum_{j} \mathbf{W}_{(i, j)}$，$\mathbf{W}_{(i, j)}$ 表示的是不同 frame 之间的相似程度。增加两个辅助变量，以及考虑到 CNN 的输出是残差，可以设计出如下的目标函数：

<img src="http://latex.codecogs.com/svg.latex? \mathcal{C}=\|\mathcal{A}(\mathbf{X})-\mathbf{B}\|_{2}^{2}+\lambda_{1}\|\mathbf{X}-\mathbf{Y}\|^{2}+  \lambda_{2}\left(\operatorname{tr}\left(\mathbf{X}^{T} \mathbf{D} \mathbf{X}\right)+\operatorname{tr}\left(\mathbf{Z}^{T} \mathbf{D} \mathbf{Z}\right)-2 \operatorname{tr}\left(\mathbf{X}^{T} \mathbf{W} \mathbf{Z}\right)\right)" border="0"/>

其中：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{Y}=\mathcal{D}_{\mathbf{w}}(\mathbf{X}) \text { and } \mathbf{Z}=\mathbf{X}" border="0"/>

unroll 网络的表达形式：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{x}_{n+1}^{(i)}=\left[\mathbf{A}_{i}^{H} \mathbf{A}_{i}+\left(\lambda_{1}+\lambda_{2} d_{i}\right) \mathbf{I}\right]^{-1}\left(\mathbf{A}_{i}^{H} \mathbf{B}+\lambda_{1} \mathbf{y}_{n}^{(i)}+\lambda_{2} \mathbf{q}_{n}^{(i)}\right)" border="0"/>

具体的计算过程：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \mathbf{Y}_{n} &=\mathcal{D}_{\mathbf{w}}\left(\mathbf{X}_{n}\right) \\ \mathbf{Q}_{n} &=\mathbf{W} \mathbf{Z}_{n}=\mathbf{W} \mathbf{X}_{n} \\ \mathbf{R}_{n} &=\left(\mathcal{A}^{*}(\mathbf{B})+\lambda_{1} \mathbf{Y}_{n}+\lambda_{2} \mathbf{Q}_{n}\right) \\ \mathbf{X}_{n+1} &=\left(\mathcal{A}^{*} \mathcal{A}+\lambda_{1} \mathbf{I}+\lambda_{2} \mathbf{D}\right)^{-1} \mathbf{R}_{n} \end{aligned}" border="0"/>

其中，$Q_n$ 的计算就是独立的，和其他参数不一起更新。

### Bibtex:

```latex
@inproceedings{biswas2018model,
  title={Model-based free-breathing cardiac MRI reconstruction using deep learned \& storm priors: MoDL-storm},
  author={Biswas, Sampurna and Aggarwal, Hemant K and Poddar, Sunrita and Jacob, Mathews},
  booktitle={2018 IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP)},
  pages={6533--6537},
  year={2018},
  organization={IEEE}
}
```

