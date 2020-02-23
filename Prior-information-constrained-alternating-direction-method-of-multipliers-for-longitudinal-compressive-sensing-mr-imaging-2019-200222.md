### Paper:

Prior information constrained alternating direction method of multipliers for longitudinal compressive sensing MR imaging

### Author:

Ruirui Kang, Danni Ai, Gangrong Qu, Qingbo Li, Xu Li, Yurong Jiang, Yong Huang, Hong Song, Yongtian Wang, Jian Yang

### Year:

2019

### Notes:

阅读日期：2020.02.22

泛读。这篇文章研究的是 MRI 重建，用的方法是传统的 ADMM 算法，没有使用深度学习的方法。作者提出的方法的创新点在于除了一般的小波变换1范数之外，增加了一个和参考图像的差的1范数。参考图像可以是其他 contrast 的图像。ADMM 算法没有什么特别之处，是按照正常的算法计算。另外两个1范数都有一个权重参数，作者也给出了计算的公式。

目标函数：

<img src="http://latex.codecogs.com/svg.latex? \min _{\mathbf{x}}\left\|\mathbf{F}_{u} \mathbf{x}-\mathbf{b}\right\|_{2}^{2}+\lambda_{1}\left\|\mathbf{W}_{1} \mathbf{\Phi}^{T} \mathbf{x}\right\|_{1}+\lambda_{2}\left\|\mathbf{W}_{2}\left(\mathbf{x}-\mathbf{x}_{0}\right)\right\|_{1}" border="0"/>

权重W计算公式：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} w_{1}^{i} &=\frac{1}{1+\left[\left|\Phi^{T} \mathbf{x}\right|\right]_{i}} \\ w_{2}^{i} &=\frac{1}{1+\left[\left|\mathbf{x}-\mathbf{x}_{0}\right|\right]_{i}} \end{aligned}" border="0"/>

### Bibtex:

```latex
@article{kang2020prior,
  title={Prior information constrained alternating direction method of multipliers for longitudinal compressive sensing MR imaging},
  author={Kang, Ruirui and Ai, Danni and Qu, Gangrong and Li, Qingbo and Li, Xu and Jiang, Yurong and Huang, Yong and Song, Hong and Wang, Yongtian and Yang, Jian},
  journal={Neurocomputing},
  volume={376},
  pages={128--140},
  year={2020},
  publisher={Elsevier}
}
```

