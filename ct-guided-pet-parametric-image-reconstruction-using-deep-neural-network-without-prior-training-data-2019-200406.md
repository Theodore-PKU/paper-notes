### Paper:

CT-guided PET parametric image reconstruction using deep neural network without prior training data

### Author:

Jianan Cui, Kuang Gong, Ning Guo, Kyungsang Kim, Huafeng Liu and Quanzheng Li

### Year:

2019

### Notes:

泛读。这篇文章研究的是 PET parametric image 重建。但是没有弄明白重建的物理模型是什么，仅仅知道最后仍然可以化成一个线性的目标函数。这篇文章的方法是 DIP，也就是将重建的图像控制为神经网络的输出，使其满足模型推出的目标函数，这是一个无监督的方法，所以题目称为 without prior training data。网络结构是 3D U-net，有一点点细节上的变动，网络的输入是 CT 图像（所以叫 CT-guided，作者表示是创新之一）。作者说是第一个将 DNN 应用于 PET parametric image 重建的文章。DIP 作为约束条件，用 ADMM 算法求解，其中的一个子问题用了替代函数（优化上的技巧吧，这个方法在 PET 重建中也见过多次了）。方法分类应该属于无监督方法中的类似生成模型+搜索的方法。

目标函数：
$$
\begin{aligned}
&\text { minimize } \quad \frac{1}{2}\|\boldsymbol{Y}-\boldsymbol{P} \boldsymbol{K}\|^{2}\\
&\text { subject to } \quad \boldsymbol{D} \boldsymbol{V} \boldsymbol{R}=f(\boldsymbol{\theta} | \boldsymbol{z})
\end{aligned}
$$

### Bibtex:

```latex
@inproceedings{cui2019ct,
  title={CT-guided PET parametric image reconstruction using deep neural network without prior training data},
  author={Cui, Jianan and Gong, Kuang and Guo, Ning and Kim, Kyungsang and Liu, Huafeng and Li, Quanzheng},
  booktitle={Medical Imaging 2019: Physics of Medical Imaging},
  volume={10948},
  pages={109480Z},
  year={2019},
  organization={International Society for Optics and Photonics}
}
```

### 其他

