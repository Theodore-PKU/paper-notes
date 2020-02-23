### Paper:

Self-Supervised Physics-Based Deep Learning MRI Reconstruction Without Fully-Sampled Data

### Author:

Burhaneddin Yaman, Seyed Amir Hossein Hosseini, Steen Moeller, Jutta Ellermann, Kâmil Ugurbil and Mehmet Akçakaya

### Year:

2019

### Notes:

阅读日期：2020.02.21

泛读。这篇文章研究的是 MRI 重建，作者的思路是在没有 groundtruth 的情况下进行训练。主要的创新点在于损失函数的设计。作者把 undersample 的 kspace 的 index 分为两个部分，一个部分对应的 kspace 用来构建训练数据的测量矩阵、测量值，另一个部分用来检验重建效果，也就是构建损失函数，即重建的 kspace 的另一部分 index 要接近。相当于用更少的测量值数据来训练了。重建部分是一个 unroll 的网络结构，也是基于 proximal 算子的 CNN + DC 的迭代结构。实验中 CNN 用的是 ResNet，这些细节就不在意了。最终的实验结果，确实这种 self-supervised 的方式只比标准的 supervised 差一点点。

假设 undersample 的部分是 $\Omega$，则 index 划分是：

<img src="http://latex.codecogs.com/svg.latex? \Omega=\Theta \cup \Lambda" border="0"/>

那么构建的损失函数是：

<img src="http://latex.codecogs.com/svg.latex? \min _{\boldsymbol{\theta}} \frac{1}{N} \sum_{i=1}^{N} \mathcal{L}\left(\mathbf{y}_{\Lambda}^{i}, \mathbf{E}_{\Lambda}^{i}\left(f\left(\mathbf{y}_{\Theta}^{i}, \mathbf{E}_{\Theta}^{i} ; \boldsymbol{\theta}\right)\right)\right)" border="0"/>

从这个损失函数的表达式就可以看出作者的想法。注意训练的下标和比较对象的下标。$f$ 在这里表示重建算法，$E$ 表示测量矩阵。下标则表示对应不同的 index。

### Bibtex:

```latex
@article{yaman2019self,
  title={Self-Supervised Physics-Based Deep Learning MRI Reconstruction Without Fully-Sampled Data},
  author={Yaman, Burhaneddin and Hosseini, Seyed Amir Hossein and Moeller, Steen and Ellermann, Jutta and Uǧurbil, K{\^a}mil and Ak{\c{c}}akaya, Mehmet},
  journal={arXiv preprint arXiv:1910.09116},
  year={2019}
}
```

