### Paper:

Deep Dictionary-transform Learning for Image Reconstructon

### Author:



### Year:

2018

### Notes:

阅读日期：2020.01.18

泛读。这篇文章研究的是字典学习的一种拓展，实验的对象是 MRI 数据。作者首先叙述了 UTMRI 算法，然后利用 UTMRI 算法的结构特点展开为有监督的参数学习（卷积参数，不过即使不是 UTMRI 算法，也很容易得到这个框架）。简单来说，作者的做法就是将字典变换、阈值和逆变换用卷积层的形式来表示（见示意图），然后加上数据 fidelity 计算。和 cascade 框架不同的就主要是卷积层的运用不太一样。另外训练的时候是逐层训练。

损失函数是：

<img src="http://latex.codecogs.com/svg.latex? \min _{D^{k}, W^{k}, \Gamma^{k}}\left\|P^{\operatorname{train}}-D^{k} S_{\Gamma^{k}}\left(W^{k} \hat{P}^{k-1}\right)\right\|_{1}+\beta \tilde{R}\left(D^{k}\right)" border="0"/>

$P$ 是数据，$D$ 和 $W$ 分别是两个卷积层，$\tilde{R}(D)$ 是对 $D$ 的一个正则项 $\tilde{R}\left(D^{k}\right) \triangleq \sum_{l=1}^{L}\left(\left\|d_{l}^{k}\right\|_{2}^{2}-1\right)^{2}$，$S_{\Gamma}$ 是soft-thresholding函数。

示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-18%E4%B8%8B%E5%8D%885.30.43.png"/>

### Bibtex:

```latex
@inproceedings{ravishankar2018deep,
  title={Deep dictionary-transform learning for image reconstruction},
  author={Ravishankar, Saiprasad and Lahiri, Anish and Blocker, Cameron and Fessler, Jeffrey A},
  booktitle={2018 IEEE 15th International Symposium on Biomedical Imaging (ISBI 2018)},
  pages={1208--1212},
  year={2018},
  organization={IEEE}
}
```

