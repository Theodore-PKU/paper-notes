### Paper:

Highly Scalable Image Reconstruction using Deep Neural Networks with Bandpass Filtering

### Author:

Joseph Y. Cheng, Feiyu Chen, Marcus T. Alley, John M. Pauly, and Shreyas S. Vasanawala

### Year:

2018

### Notes:

阅读日期：2020.01.10

泛读。这篇文章研究的是多线圈的MRI重建。作者的关注点并不是让重建效果更好，而是考虑如何让计算更快。作者的想法是用patch 计算的时候可以采用并行的方式，这样可以大大加快运行速度。但是一般的框架下，使用 patch，对于 MRI 问题而言，很难利用 data fidelity 的性质来增加重建的准确度。这似乎是一个矛盾。而作者提出的基于 patch 的方法就是要解决这个矛盾。作者的具体做法是对 kspace 的patch进行处理，优化的目标函数就和以往不同。当然在这个目标函数下，作者还是使用了 ISTA 算法展开为深度学习训练的方式，仍然有 data fidelity 项和 denoise 项交替。由于作者考虑的是 kspace 的 patch，所以对于具体使用什么样的网络结构，并不关心（作者在文章中给出了自己的做法）。

作者考虑的局部的 kspace 的表达式：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{u}_{i}=\mathbf{M}_{i} \mathbf{A}\left(e^{j 2 \pi\left(\mathbf{k}_{i} \cdot \mathbf{x}\right)} * \mathbf{y}_{i}\right)"  border="0"/>

其中，<img src="http://latex.codecogs.com/svg.latex? \mathbf{y}_{i}"  border="0"/>是图像，<img src="http://latex.codecogs.com/svg.latex? \mathbf{M}_{i}"  border="0"/>是抽取位置的矩阵运算，A 是敏感矩阵和傅立叶变换。由此得到的目标函数是：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned}\hat{\mathbf{y}}_{i}=\arg \min _{\mathbf{y}_{i}} \| \mathbf{W}\left[\mathbf{M}_{i} \mathbf{A}\left(e^{j 2 \pi\left(\mathbf{k}_{i} \cdot \mathbf{x}\right)} * \mathbf{y}_{i}\right)-\mathbf{u}_{i}\right] \|_{2}^{2} +\lambda R\left(\mathbf{y}_{i}\right)\end{aligned}"/>

神经为网络的输入是 kspace patch 和用传统方法重建的初始值，输出则是更新后的 kspace patch。不过这里有一个问题，<img src="http://latex.codecogs.com/svg.latex? \mathbf{y}_{i}"  border="0"/>仍然是整张图像，这样的处理似乎也不会快多少？虽然在傅立叶变换的计算上确实少了很多。

网络的block示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-10%E4%B8%8B%E5%8D%889.44.12.png" width="50%"/>

### Bibtex:

```latex
@article{cheng2018highly,
  title={Highly Scalable Image Reconstruction using Deep Neural Networks with Bandpass Filtering},
  author={Cheng, Joseph Y and Chen, Feiyu and Alley, Marcus T and Pauly, John M and Vasanawala, Shreyas S},
  journal={arXiv preprint arXiv:1805.03300},
  year={2018}
}
```

