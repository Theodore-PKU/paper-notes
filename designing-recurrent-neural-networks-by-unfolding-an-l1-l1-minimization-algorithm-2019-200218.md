### Paper:

Designing Recurrent Neural Networks by Unfolding an L1-L1 Minimization Algorithm

### Author:

Hung Duy Le, Huynh Van Luong, Nikos Deligiannis

### Year:

2019

### Notes:

阅读日期：2020.02.18

泛读。这篇文章研究的是 vedio 压缩感知（这是作者的测试任务）。使用的方法是还是 unroll 一个传统算法，正如题目中的 L1-L1 的字面意思，目标函数包含两个 l1 范数。传统的算法是 ISTA 算法，proximal 算子因为两个 l1 范数的缘故，和 thresholding 是不一样的。在 ISTA 算法中，测量矩阵、字典等相关的参数都是已知的，而在作者提出的网络学习中，都是可学习的参数。因为有 temperal 的 1 范数，所以结构上类似于 RNN。感觉参考价值不大，因为思路太普通了。另外，作者没有用什么卷积结构，那些矩阵之所以可以比较方便的学习是因为作者用的数据 size 很小。

<img src="http://latex.codecogs.com/svg.latex? \min _{\boldsymbol{h}_{t}} \frac{1}{2}\left\|\boldsymbol{x}_{t}-\mathbf{A} \mathbf{D} \boldsymbol{h}_{t}\right\|_{2}^{2}+\lambda_{1}\left\|\boldsymbol{h}_{t}\right\|_{1}+\lambda_{2}\left\|\boldsymbol{h}_{t}-\mathbf{G} \boldsymbol{h}_{t-1}\right\|_{1}" border="0"/>

求解算法也很好理解，proximal gradient method，注意因为重建的信号是序列，所以前一个信号和当前信号的重建是有关的。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200218131621.png" width="70%"/>

proximal 算子比较复杂：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200218131927.png" width="60%"/>

在作者的方法中，可学习参数有 ${\theta}=\left\{\mathbf{A}, \mathbf{D}, \mathbf{G}, \mathbf{h}_{0}, \alpha, \lambda_{1}, \lambda_{2}\right\}$，几乎所有的参数都是训练的。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200218132153.png" width="60%"/>

### Bibtex:

```latex
@inproceedings{le2019designing,
  title={Designing recurrent neural networks by unfolding an l1-l1 minimization algorithm},
  author={Le, Hung Duy and Van Luong, Huynh and Deligiannis, Nikos},
  booktitle={2019 IEEE International Conference on Image Processing (ICIP)},
  pages={2329--2333},
  year={2019},
  organization={IEEE}
}
```

