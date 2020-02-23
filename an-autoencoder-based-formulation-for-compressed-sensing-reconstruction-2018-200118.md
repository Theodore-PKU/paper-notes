### Paper:

An autoencoder based formulation for compressed sensing reconstruction

### Author:

Angshul Majumdar

### Year:

2018

### Notes:

阅读日期：2020.01.18

泛读。这篇文章研究的是MRI 重建。作者的想法是用自编码来自适应重建图像，类似于 DL 或 TL，这种做法不需要训练数据，是在重建图像的过程中训练自编码。作者使用 ADMM 算法来求解，因为自编码器只使用了一层隐层，而且激活函数是可逆的，因此在数学上可以比较容易的求解 ADMM 的子问题。另外因为不需要 SVD 分解，求解的速度比 DL 和 TL 要快。（以及，作者在文章中说 deep learning 的方法比 CS 好，但比 DL 和 TL 要差）

DL 的优化目标函数：

<img src="http://latex.codecogs.com/svg.latex? \min _{\tilde{x}, D, Z}\|y-A \widehat{x}\|_{2}^{2}+\lambda\left(\sum_{i}\left\|P_{i} \widehat{x}-D z_{i}\right\|_{F}^{2}\right) \text { s. t. }\left\|z_{i}\right\|_{0} \leq \tau" border="0"/>

TL 的优化目标函数：

<img src="http://latex.codecogs.com/svg.latex? \min _{\tilde{\boldsymbol{x}}, \boldsymbol{T}, \boldsymbol{Z}}\|y-A \widehat{x}\|_{2}^{2}+\lambda\left(\sum_{i}\left\|T P_{i} \widehat{x}-z_{i}\right\|_{F}^{2} \right)\text { s.t. }\left\|z_{i}\right\|_{0} \leq \tau" border="0"/>

自编码器的优化目标函数：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned}\min _{W^{\prime}, W, \widehat{x}}\|x-A \widehat{x}\|_{2}^{2}+\lambda \sum_{i}\left(\left\|P_{i} \widehat{x}-W^{\prime} \varphi\left(W P_{i} \widehat{x}\right)\right\|_{2}^{2}+\mu\left\|\varphi\left(W P_{i} \widehat{x}\right)\right\|_{1}\right)\end{aligned}" border="0"/>

个人感觉自编码器有点像 DL 和 TL 的结合。

ADMM 算法的求解步骤：

1. least squares problem

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-18%E4%B8%8B%E5%8D%887.43.15.png" width="30%"/>

2. least squares problem

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-18%E4%B8%8B%E5%8D%887.43.20.png" width="60%"/>

3. least squares problem

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-18%E4%B8%8B%E5%8D%887.43.02.png" width="60%"/>

4.  $l_1$-regularized least squares problem，using iterative soft thresholding algorithm

   <img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-18%E4%B8%8B%E5%8D%887.43.08.png" width="60%"/>

### Bibtex:

```latex
@article{majumdar2018autoencoder,
  title={An autoencoder based formulation for compressed sensing reconstruction},
  author={Majumdar, Angshul},
  journal={Magnetic resonance imaging},
  volume={52},
  pages={62--68},
  year={2018},
  publisher={Elsevier}
}
```

