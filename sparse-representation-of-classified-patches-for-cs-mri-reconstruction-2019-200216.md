### Paper:

Sparse representation of classiﬁed patches for CS-MRI reconstruction

### Author:

Jianxin Cao, Shujun Liu, Hongqing Liu, Xiaoheng Tan, Xichuan Zhou

### Year:

2019

### Notes:

阅读日期：2020.02.16

泛读。这篇文章研究的是 MRI 的重建，没有深度学习的方法，而是一个传统的字典学习的思路。不过和传统的字典学习不同，作者考虑到只使用一个字典时，需要 overcomplete atoms，这会导致计算量大和 atoms 之间的相关性的问题，因此，作者试图使用多个字典，分别用于不同的 patch。所以作者的做法首先对 patch 进行聚类，使用的是 spherical k-means 的方法，这个方法比普通的 k-means 更有效地度量相似性，不会遇到一些不符合常识的问题。对于每一类 patch，用单独的一个字典来表示。这些字典都有正交性的约束。重建算法使用了 ADM 的解法，目标函数中的稀疏性用的是 $l_p$ 范数。子问题的求解没有细看。另外，作者还提到对 image 先做一个预处理，再进行聚类。这里的 image 是先用 FCAS 计算出来的。分出 patch 之后，似乎就和这个预处理没关系了。

聚类及预处理算法：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200216195728.png"/>

是否有预处理的效果差别：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200216195733.png"/>

目标函数（这里的 $k$ 表示不同的类，$A_{C_k},\tilde{R}_{C_{k}}$ 也是同理）：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\min _{\mathbf{x}, \mathbf{A}, \mathbf{D}} \lambda\left\|\boldsymbol{y}-\mathbf{F}_{\mathbf{u}} \boldsymbol{x}\right\|_{2}^{2}+\sum_{k=1}^{K}\left\{\beta\left\|\tilde{\mathbf{R}}_{C_{k}} \boldsymbol{x}-\mathbf{D}_{k} \mathbf{A}_{C_{k}}\right\|_{F}^{2}+\left\|\mathbf{A}_{C_{k}}\right\|_{p}^{p}\right\}} \\ {\text { s.t. } \mathbf{D}_{k}^{H} \mathbf{D}_{k}=\mathbf{I}, \forall k}\end{array}" border="0"/>

关于具体的结果，只说明一下 ADM 算法的思路。

The core idea behind ADM is that initializing $β$ to a small number ﬁrst, and then minimizing Eq. (8) with respect to one variable while keeping other variables ﬁxed, and iterating this process along with gradually increased $β$ until convergence.

### Bibtex:

```latex
@article{cao2019sparse,
  title={Sparse representation of classified patches for CS-MRI reconstruction},
  author={Cao, Jianxin and Liu, Shujun and Liu, Hongqing and Tan, Xiaoheng and Zhou, Xichuan},
  journal={Neurocomputing},
  volume={339},
  pages={255--269},
  year={2019},
  publisher={Elsevier}
}
```

