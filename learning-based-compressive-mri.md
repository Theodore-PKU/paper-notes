### Paper:

Learning-Based Compressive MRI

### Author:

Baran Gozcu, Rabeeh Karimi Mahabadi, Yen-Huan Li, Efe Ilıcak, Tolga Cukur, Jonathan Scarlett, and Volkan Cevher

### Year:

2018

### Notes:

这篇文章考虑的问题是什么样的欠采样方式是最合适的。作者的想法很简单，固定重建方法，什么样的采样方式能够使得该重建方法的结果最好，那么这样的采样方法就是好的。如何判定呢？用数据来学，也就是用数据集平均的表现来判定。从组合数学的角度来说，采样方式非常多，怎么找最好的？用贪婪的方法（类似统计上的选择变量）。

假设重建算法是 $g()$，判断重建效果的函数为 $\eta()$，那么用下式来表示在某个数据集上的采样方式 $\Omega$ 的表现。
$$
\frac{1}{m} \sum_{j=1}^{m} \eta_{\Omega,g}\left(\mathbf{x}_{j}\right)
$$
如果对采样方式有限制，一般来说可以把采样点的子集作为基本的增减单位，采样方式 $\Omega$ 可以表示为：
$$
\Omega=\bigcup_{j=1}^{\ell} S_{j}, \quad S_{j} \in \mathcal{S}
$$
增加 $\Omega$ 的采样点时要注意不能超过采样方式的限制。从理论上说，只要训练数据代表了数据分布，那么这种做法计算出来的 $\frac{1}{m} \sum_{j=1}^{m} \eta_{\Omega,g}\left(\mathbf{x}_{j}\right)$ 和真实的表现有误差上界。

贪婪的意思是说，每次在下一个要增加的子集中选择表现最好的。类似地，也可以考虑不断减少选择的子集或者加减同时存在。

如果 fully sample 有噪声的情形，对 fully sample 重建的信号先做去噪处理。

作者做实验的实验用了各种不同的重建方法，比较最终的相应采样方式的差别。比较有意思的是，每种重建算法都是用自己的算法重建的结果去找的采样效果最好。（那不是写文章的时候，如果采样方式挑得好，效果就提升，其他人的方法的效果就差了？换句话说，如果给定了采样方式，也有一种最好的重建算法了。但不一定是效果最好的。同时换一种采样，换一种重建，效果更好）。另外采样的结果都是高频的部分采样的非常少。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-27%E4%B8%8B%E5%8D%889.50.47.png" style="zoom: 67%;" />

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-27%E4%B8%8B%E5%8D%889.50.27.png" style="zoom:50%;" />

### Bibtex:

```latex
@article{gozcu2018learning,
  title={Learning-based compressive MRI},
  author={G{\"o}zc{\"u}, Baran and Mahabadi, Rabeeh Karimi and Li, Yen-Huan and Il{\i}cak, Efe and {\c{C}}ukur, Tolga and Scarlett, Jonathan and Cevher, Volkan},
  journal={IEEE transactions on medical imaging},
  volume={37},
  number={6},
  pages={1394--1406},
  year={2018},
  publisher={IEEE}
}
```