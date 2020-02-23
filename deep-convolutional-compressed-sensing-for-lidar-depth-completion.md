### Paper:

Deep Convolutional Compressed Sensing for LiDAR Depth Completion

### Author:

Nathaniel Chodosh, Chaoyang Wang, Simon Lucey

### Year:

2018

### Notes:

泛读（因为应用不太相关）。这篇文章要解决的问题是2.5D的深度图的重建。模型可以写为 $\mathbf{d}_s = \mathbf{M}\mathbf{I}$。相当于只有稀疏的采样点（见下图）。因此作者把这个问题当作一个压缩感知问题来求解。采用的方法是 Deep Component Analysis (C. Murdock. “Deep Component Analysis via Alternating Direction Neural Networks”, arXiv preprint, 2018)，主要的想法是先用多层稀疏分解来表示，然后用ADMM算法的框架展开成一个神经网络，用数据训练其中的分解的字典。

2.5D深度图例子：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-02%E4%B8%8B%E5%8D%882.27.05.png" style="zoom: 50%;" />

模型：
$$
\min _{\left\{\mathbf{z}_{i}\right\}} \frac{1}{2}\left\|\mathbf{d}_{s}-\mathbf{M} \mathbf{W}_{1} \mathbf{z}_{1}\right\|_{2}^{2}+\sum_{i=2}^{\ell} \frac{1}{2}\left\|\mathbf{z}_{i-1}-\mathbf{W}_{i} \mathbf{z}_{i}\right\|_{2}^{2}+\sum_{i=1}^{\ell} \Phi_{i}\left(\mathbf{z}_{i}\right)
$$
$\mathbf{W}_{i}$  是卷积，$\Phi_{i}=I(\mathbf{x}>0)+b\|\mathbf{x}\|_{1}$ 是稀疏项



### Bibtex:

```latex
@inproceedings{chodosh2018deep,
  title={Deep convolutional compressed sensing for lidar depth completion},
  author={Chodosh, Nathaniel and Wang, Chaoyang and Lucey, Simon},
  booktitle={Asian Conference on Computer Vision},
  pages={499--513},
  year={2018},
  organization={Springer}
}
```