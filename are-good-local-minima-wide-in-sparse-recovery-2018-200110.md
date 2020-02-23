### Paper:

Are good local minima wide in sparse recovery?

### Author:

Michael Moeller and Otmar Loffeld, Jürgen Gall, Felix Krahmer

### Year:

2018

### Notes:

阅读日期：2020.01.10

泛读。这篇文章研究的问题是如何让 IHT (iterative hard thresholding) 表现得更稳定更好。IHT 算法是求解 0 范数逆问题的常见做法。作者只是考虑了增加噪声扰动的算法的实际效果。总的来说这篇文章没什么特点。不需要在意。

原始问题：

<img src="http://latex.codecogs.com/svg.latex? \min _{u}\|A u-f\|^{2} \quad \text { s.t. } \quad|u|_{0} \leq s"  border="0"/>

IHT 算法：

<img src="http://latex.codecogs.com/svg.latex? u^{k+1}=H_{s}\left(u^{k}-\tau A^{T}\left(A u^{k}-f\right)\right)"  border="0"/>

增加噪声扰动的最直接的方法是在迭代过程中加噪声，另一种是将 IHT 算法展开写成参数可学习的网络的形式，但是如果使用了全连接层，会导致参数量太多。最后采用的做法是前3000次迭代用 noisy IHT 算法，最后两次迭代用了deep learning，在深度学习这一部分 ，噪声用dropout 替代。

### Bibtex:

```latex
@article{moeller2018good,
  title={Are good local minima wide in sparse recovery?},
  author={Moeller, Michael and Loffeld, Otmar and Gall, J{\"u}rgen and Krahmer, Felix},
  journal={arXiv preprint arXiv:1806.08296},
  year={2018}
}
```