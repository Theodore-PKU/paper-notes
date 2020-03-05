### Paper:

CREAM: CNN-REgularized ADMM Framework for Compressive-Sensed Image Reconstruction

### Author:

CHEN ZHAO, JIAN ZHANG, RONGGANG WANG, AND WEN GAO

### Year:

2018

### Notes:

阅读日期：2020.02.12

泛读。这篇文章研究的是 CS 重建，其实没有什么新鲜东西了。作者提出的方法概括起来就是把 ADMM 算法中的 proximal 算子用一个去噪 CNN 替换了。另外，因为作者在推导 ADMM 算法时用了和一般不一样的形式，所以迭代的计算有一点不同。因为是去噪 CNN，所以需要事先训练，但是不需要 fine-tuning，可以直接用。以及关于噪声水平的选择，作者的做法是先训练多个噪声水平下的 CNN，然后测试出哪一个噪声水平的 CNN 平均效果最好。CNN 的结构非常简单，卷积层叠加 + residual learining。作者考虑的是 block-based 算法，但是没有说明是否有 blocky artefact 的情况。

下面给出 ADMM 算法的推导：

<img src="http://latex.codecogs.com/svg.latex? \min _{\mathbf{x}} f(\mathbf{x})+\mathbb{1}(\mathbf{x})" border="0"/>

其中：

<img src="http://latex.codecogs.com/svg.latex? \mathbb{1}(\mathbf{x})=\left\{\begin{array}{ll}{0,} & {\text { if } \mathbf{y}=\Phi \mathbf{x}} \\ {\infty,} & {\text { otherwise }}\end{array}\right." border="0"/>

根据一般的 ADMM 算法，得到：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \mathbf{x}_{j+1} &=\arg \min _{\mathbf{x}} \mathbb{1}(\mathbf{x})+\frac{\mu}{2}\left\|\mathbf{x}-\mathbf{v}_{j}-\mathbf{b}_{j}\right\|_{2}^{2} \\ \mathbf{v}_{j+1} &=\arg \min _{\mathbf{v}} f(\mathbf{v})+\frac{\mu}{2}\left\|\mathbf{x}_{j+1}-\mathbf{v}-\mathbf{b}_{j}\right\|_{2}^{2} \\ \mathbf{b}_{j+1} &=\mathbf{b}_{j}-\left(\mathbf{x}_{j+1}-\mathbf{v}_{j+1}\right) \end{aligned}" border="0"/>

关于第一个子问题，作者重新变成一个有约束问题：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{x}_{j+1}=\arg \min _{\mathbf{x}} \frac{\mu}{2}\left\|\mathbf{x}-\mathbf{z}_{j}\right\|_{2}^{2}, \quad \text { s.t. } \mathbf{y}=\Phi \mathbf{x}" border="0"/>

有闭式解：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{x}_{j+1}=\mathbf{x}^{*}=\Phi^{\top}\left(\Phi \Phi^{\top}\right)^{-1}\left(\mathbf{y}-\Phi \mathbf{z}_{j}\right)+\mathbf{z}_{j}" border="0"/>

再利用测量矩阵的性质得到：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{x}_{j+1}=\Phi^{\top} \mathbf{y}+\left(\mathbf{I}-\Phi^{\top} \Phi\right) \mathbf{z}_{j}" border="0"/>

用 CNN 替换了第二个子问题的计算后，就得到下图的框架：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200212144554.png"/>

训练 CNN 时的损失函数是：

<img src="http://latex.codecogs.com/svg.latex? \hat{\Theta}=\arg \min _{\Theta} \frac{1}{2 N_{T}} \sum_{i}^{N_{T}}\left\|g\left(\mathbf{u}^{i} ; \Theta\right)-\left(\mathbf{x}^{i}-\mathbf{u}^{i}\right)\right\|_{2}^{2}" border="0"/>

### Bibtex:

```latex
@article{zhao2018cream,
  title={CREAM: CNN-REgularized ADMM framework for compressive-sensed image reconstruction},
  author={Zhao, Chen and Zhang, Jian and Wang, Ronggang and Gao, Wen},
  journal={IEEE Access},
  volume={6},
  pages={76838--76853},
  year={2018},
  publisher={IEEE}
}
```

