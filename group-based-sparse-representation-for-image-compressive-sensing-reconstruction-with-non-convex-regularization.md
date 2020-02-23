### Paper:

Group-based Sparse Representation for Image Compressive Sensing Reconstruction with Non-Convex Regularization

### Author:

Zhiyuan Zha, Xinggan Zhang, Qiong Wang, Lan Tang, Xin Liu

### Year:

2017

### Notes:

泛读。

这篇文章的主要想法是类似于 tensor factor analysis，也就是稀疏性应该建立在相似patch 之上（把patch 拉直为向量后拼成矩阵 ）。另外作者考虑的正则项约束是 p 范数, $0<p<1$。

设测量值 $Z_G$, 字典 $D_G$，测量矩阵 $\phi$, 系数$\alpha_G$，作者的目标函数就是：
$$
\boldsymbol{\alpha}_{G}=\arg \min _{\boldsymbol{\alpha}_{G}} \frac{1}{2}\left\|\boldsymbol{Z}_{G}-\boldsymbol{\phi} \boldsymbol{D}_{G} \boldsymbol{\alpha}_{G}\right\|_{2}^{2}+\left\|\boldsymbol{W}_{G} \cdot \alpha_{G}\right\|_{p}
$$
作者提出了一种 Iterative Shrinkage/Thresholding (IST) Algorithm 来求解这个问题。其中字典的计算是用PCA方法得到（论文中没有说明），patch 的相似性也没有说明怎么度量。具体算法不写了，论文中有一些细节的错误。



### Bibtex:

```
@article{zha2018group,
  title={Group-based sparse representation for image compressive sensing reconstruction with non-convex regularization},
  author={Zha, Zhiyuan and Zhang, Xinggan and Wang, Qiong and Tang, Lan and Liu, Xin},
  journal={Neurocomputing},
  volume={296},
  pages={55--63},
  year={2018},
  publisher={Elsevier}
}
```