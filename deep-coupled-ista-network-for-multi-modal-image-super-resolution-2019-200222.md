### Paper:

Deep Coupled ISTA Network for Multi-modal Image Super-Resolution

### Author:

Xin Deng, Student Member, and Pier Luigi Dragotti

### Year:

2019

### Notes:

阅读日期：2020.02.22

泛读。这篇文章研究的是超分辨，不过利用的是多模态的信息。具体而言，就是从一个 LR 图像 x，在另一个 HR 图像 y（同一个内容，但不同模态）的帮助下生成 SR 图像 z。整体上借助了字典学习的框架。最原始的做法是先训练好三个图像对应的三个字典。作者假设 HR 图像的 code 可以用另外两个图像的 code 计算出来。在实际的 LR 到 HR 重建的过程中，只需要将 x 和 y，算出对应的 code，就可以重建出 z 的 code，然后再利用 z 的字典得到 HR 图像。题目所谓的 deep coupled ista，指的是将后面的重建算法展开成 ISTA net 的形式。x，y 的计算因为可以分开，所以是两支网络。利用这个网络，作者设计了一个逐层训练的方式，训练的参数就包括 x，y，z 的字典，x，y，z 的 code 的变换矩阵等。

作者假设的 x, y, z 的 code 的关系：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{c}=S_{\boldsymbol{\lambda}_{c}}\left(\boldsymbol{W}_{x} \boldsymbol{a}+\boldsymbol{W}_{y} \boldsymbol{b}\right)" border="0"/>

用如下的优化问题求出变换矩阵 $W_x, W_y$ 和字典。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200223153241.png" width="60%"/>

这个问题的求解用 alternating strategy 求解。

实际问题则是求解 x，y 的code，然后计算出 z。

<img src="http://latex.codecogs.com/svg.latex? \min _{\{\boldsymbol{a}, \boldsymbol{b}\}} \frac{1}{2}\left\|\boldsymbol{x}-\boldsymbol{D}_{x} \boldsymbol{a}\right\|_{2}^{2}+\frac{1}{2}\left\|\boldsymbol{y}-\boldsymbol{D}_{y} \boldsymbol{b}\right\|_{2}^{2}+\nu_{x}\|\boldsymbol{a}\|_{1}+\nu_{y}\|\boldsymbol{b}\|_{1}" border="0"/>

这个问题的求解，其实 x，y 是完全分开的，而且用 ISTA 算法就可以求了。只不过最后有一个利用变换矩阵计算出 z 的过程。作者提出的逐层训练的方式就忽略了，因为这种方法我认为不好。特别是 code 的假设。

最后，需要说明的是，作者用的都是 patch。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200223153753.png"/>

### Bibtex:

```latex
@article{deng2019deep,
  title={Deep Coupled ISTA Network for Multi-Modal Image Super-Resolution},
  author={Deng, Xin and Dragotti, Pier Luigi},
  journal={IEEE Transactions on Image Processing},
  volume={29},
  pages={1683--1698},
  year={2019},
  publisher={IEEE}
}
```

