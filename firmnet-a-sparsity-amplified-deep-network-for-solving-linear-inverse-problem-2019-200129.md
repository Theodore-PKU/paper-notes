### Paper:

Firmnet A Sparsity Amplified Deep Network for Solving Linear Inverse Problem

### Author:

Praveen Kumar Pokala, Amol G Mahurkar, and Chandra Sekhar Seelamantul

### Year:

2019

### Notes:

阅读时间：2020.01.29

泛读。这篇文章研究的是一般的逆问题重建，作者思考的角度是传统算法中的罚项。作者认为，在实际效果上，传统算法使用 l1 范数，会有一些问题，比如 support 较大，一些较大的值会被低估等，为了克服这一困难，前人提出了一个替代 l1 范数的罚项。在这篇文章中，作者针对个罚项 MC penalty，提出了一个迭代算法 Iterative Firm Thresholding Algorithm (IFTA)，以及相对应的 deep network FirmNet。在评价指标方面，作者使用了 PES（评价 support 的准确性）。实验上，作者用的是模拟数据，都是一维的理想数据进行数值模拟，作者在论文中表示，其 suport 的准确性比 ISTA 算法要好。

IFTA 算法的目标函数：

<img src="http://latex.codecogs.com/svg.latex? \arg \min _{\mathbf{x}} \underbrace{\|\mathbf{y}-D \mathbf{x}\|_{2}^{2}}_{f(\mathbf{x})}+g_{\gamma}(\mathbf{x} ; \lambda)" border="0"/>

其中，第二项表示 MC penalty，其表达形式为：

<img src="http://latex.codecogs.com/svg.latex? g_{\gamma}(\mathbf{x} ; \lambda)=\sum_{i=1}^{n} g_{\gamma}\left(x_{i} ; \lambda\right)\\g_{\gamma}\left(x_{i} ; \lambda\right)=\left\{\begin{array}{ll}{\lambda\left(\left|x_{i}\right|-\frac{x_{i}^{2}}{2 \gamma \lambda}\right),} & {\left|x_{i}\right| \leq \gamma \lambda} \\ {\frac{\gamma \lambda^{2}}{2},} & {\left|x_{i}\right| \geq \lambda \gamma}\end{array}\right." border="0"/>

迭代算法：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-29%E4%B8%8A%E5%8D%8811.16.49.png" width="50%"/>

其中，

<img src="http://latex.codecogs.com/svg.latex? F_{\gamma}^{g}\left(u_{i} ; \lambda\right)=\left\{\begin{array}{ll}{0,} & {\left|u_{i}\right| \leq \lambda} \\ {\frac{\gamma}{\gamma-1}\left(\left|u_{i}\right|-\lambda\right) \operatorname{sign}\left(u_{i}\right),} & {\lambda \leq \left|u_{i}\right| \leq \gamma \lambda} \\ {u_{i},} & {\left|u_{i}\right|>\gamma \lambda}\end{array}\right." border="0"/>

转换成网络后，参数 $U, V, \lambda, \eta$ 都是可学习的，但是作者没有说是否有参数共享，也没有说清 $U, V$ 是否的都是矩阵形式（估计就单纯是矩阵）。网络结构就是一个 recursive 形式的网络。示意图如下（上图是迭代算法，下图是对应的网络）：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-29%E4%B8%8A%E5%8D%8811.12.27.png" width="50%"/>

实验结果 PES，和 ISTA 算法对比，蓝色表示误差小：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-29%E4%B8%8A%E5%8D%8811.12.18.png" width="50%"/>

### Bibtex:

```latex
@inproceedings{pokala2019firmnet,
  title={FirmNet: A Sparsity Amplified Deep Network for Solving Linear Inverse Problems},
  author={Pokala, Praveen Kumar and Mahurkar, Amol G and Seelamantula, Chandra Sekhar},
  booktitle={ICASSP 2019-2019 IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP)},
  pages={2982--2986},
  year={2019},
  organization={IEEE}
}
```

