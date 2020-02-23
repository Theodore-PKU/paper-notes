### Paper:

MAPEM-Net: An Unrolled Neural Network for Fully 3D PET Image Reconstruction

### Author:

Kuang Gong, Dufan Wu, Kyungsang Kim, Jaewon Yang, Tao Sun, Georges El Fakhri, Youngho Seo, Quanzheng Li

### Year:

2019

### Notes:

阅读日期：2020.02.19

泛读。这篇文章研究 PET 重建，使用方法仍然是展开一个传统算法。传统算法的目标函数是 data fidelity（log 似然函数）+ 正则项。作者用 ADMM 算法求解（之前作者的一篇文章是直接用 gradient），CNN 的部分还是替换了原来的 proximal 算子部分。不过 proximal 算子的前一步在这里执行了两次。CNN 用的是 U-net，没有什么特别的。训练方式也是 final reconstruction 和 groundtruth 的 2 范数。

目标函数：

<img src="http://latex.codecogs.com/svg.latex? \max _{\boldsymbol{x}, \boldsymbol{z}} L(\boldsymbol{y} | \boldsymbol{x})-\beta U(\boldsymbol{z}), \text { s.t. } \boldsymbol{x}=\boldsymbol{z}" border="0"/>

迭代算法：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \boldsymbol{x}^{n+1} &=\arg \max L(\boldsymbol{y} | \boldsymbol{x})-\rho\left\|\boldsymbol{x}-\boldsymbol{z}^{n}+\boldsymbol{\mu}^{n}\right\|_{2} \\ \boldsymbol{z}^{n+1} &=\arg \min \left\|\boldsymbol{x}^{n+1}-\boldsymbol{z}+\boldsymbol{\mu}^{n}\right\|_{2}+\beta U(\boldsymbol{z}) \\ \boldsymbol{\mu}^{n+1} &=\boldsymbol{\mu}^{n}+\boldsymbol{x}^{n+1}-\boldsymbol{z}^{n+1} \end{aligned}" border="0"/>

第一步称为 MAPEM 步，比较复杂，但是直接计算的：

<img src="http://latex.codecogs.com/svg.latex? x_{j}^{n+1}=\frac{1}{2}\left[z_{j}^{n}-\mu_{j}^{n}-P_{. j} / \rho\right]+\frac{1}{2} \sqrt{\left[z_{j}^{n}-\mu_{j}^{n}-P_{. j} / \rho\right]^{2}+4 x_{j}^{n} \hat{x}_{j, \text { temp }}^{n+1} / \rho}" border="0"/>

其中：

<img src="http://latex.codecogs.com/svg.latex? \hat{\boldsymbol{x}}_{\mathrm{temp}}^{n+1}=\boldsymbol{P}^{T} \frac{\boldsymbol{y}}{\boldsymbol{P} \boldsymbol{x}^{n}+\boldsymbol{r}}, \frac{d \hat{\boldsymbol{x}}_{\mathrm{temp}}^{n+1}}{d \boldsymbol{x}_{n}}=-\boldsymbol{P}^{T} \frac{\boldsymbol{y}}{\left(\boldsymbol{P} \boldsymbol{x}_{n}+\boldsymbol{r}\right)^{2}} \boldsymbol{P}" border="0"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200219100813.png"/>

### Bibtex:

```latex
@inproceedings{gong2019mapem,
  title={MAPEM-Net: an unrolled neural network for Fully 3D PET image reconstruction},
  author={Gong, Kuang and Wu, Dufan and Kim, Kyungsang and Yang, Jaewon and Sun, Tao and El Fakhri, Georges and Seo, Youngho and Li, Quanzheng},
  booktitle={15th International Meeting on Fully Three-Dimensional Image Reconstruction in Radiology and Nuclear Medicine},
  volume={11072},
  pages={110720O},
  year={2019},
  organization={International Society for Optics and Photonics}
}
```

