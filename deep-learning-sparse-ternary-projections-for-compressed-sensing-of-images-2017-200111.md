### Paper:

Deep Learning Sparse Ternary Projections For Compressed Sensing of Images

### Author:

Duc Minh Nguyen, Evaggelia Tsiligianni, Nikos Deligiannis

### Year:

2017

### Notes:

阅读日期：2020.01.11

泛读。这篇文章考虑的感知矩阵是三值的感知矩阵（0，1，-1）。之所以使用三值的矩阵，原因是作者认为这样对于硬件的要求更低。感知矩阵是通过训练得到的，网络分为感知部分和重建部分。关于感知部分，为了得到 3值矩阵，作者还设计了一个方案来实现（参见算法）。整个网络是用 patch 训练的。个人感觉这篇文章没什么用。

关于三值矩阵的设计，作者采用的方法是：先用 K-max select 的方式（对矩阵的每列或每行）得到支撑集，然后计算各列的支撑集上的绝对值均值，也就是1范数/K，该值作为重建时的第一层的 scale 参数（1对1的scale）。使用 scale 层的目的是让三值矩阵和 scale层复合以后和原始的感知矩阵尽量接近（之所以是绝对值的均值，就是用极小化误差计算出来的）。

网络框架：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-11%E4%B8%8B%E5%8D%889.11.17.png"/>

训练算法：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-11%E4%B8%8B%E5%8D%889.11.25.png" width="60%"/>

### Bibtex:

```latex
@inproceedings{nguyen2017deep,
  title={Deep learning sparse ternary projections for compressed sensing of images},
  author={Nguyen, Duc Minh and Tsiligianni, Evaggelia and Deligiannis, Nikos},
  booktitle={2017 IEEE Global Conference on Signal and Information Processing (GlobalSIP)},
  pages={1125--1129},
  year={2017},
  organization={IEEE}
}
```

