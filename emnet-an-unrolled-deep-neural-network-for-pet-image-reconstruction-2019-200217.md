### Paper:

EMnet: An Unrolled Deep Neural Network for PET Image Reconstruction

### Author:

Kuang Gong, Dufan Wu, Kyungsang Kim, Jaewon Yang, Georges El Fakhri, Youngho Seo, Quanzheng Li

### Year:

2019

### Notes:

阅读日期：2020.02.17

泛读。这篇文章研究的是 PET 的重建，使用的方法是展开 EM 算法，在原始的 EM 算法中，可以通过改写迭代式得到一个类似于梯度的迭代式，如果加入一个正则项，在迭代时相当于加入一个正则项的梯度。作者用 U-net 表示这个梯度，由此得到一个展开网络。

PET 模型：

<img src="http://latex.codecogs.com/svg.latex? \overline{\boldsymbol{y}}=\boldsymbol{P} \boldsymbol{x}+\boldsymbol{r}" border="0"/>

PET 的对数似然函数：

<img src="http://latex.codecogs.com/svg.latex? L(\boldsymbol{y} | \boldsymbol{x})=\sum_{i=1}^{M} y_{i} \log \bar{y}_{i}-\bar{y}_{i}-\log y_{i} !" border="0"/>

EM 算法的迭代式：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{x}^{n+1}=\frac{\boldsymbol{x}^{n}}{\boldsymbol{P}^{T} \boldsymbol{I}_{S}} \boldsymbol{P}^{T} \frac{\boldsymbol{y}}{\boldsymbol{P} \boldsymbol{x}^{n}+\boldsymbol{r}}" border="0"/>



可以改写成：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{x}^{n+1}=\boldsymbol{x}^{n}-\frac{\boldsymbol{x}^{n}}{\boldsymbol{P}^{T} \boldsymbol{I}_{S}}\left[\boldsymbol{P}^{T}\left(\boldsymbol{I}_{S}-\frac{\boldsymbol{y}}{\boldsymbol{P} \boldsymbol{x}^{n}+\boldsymbol{r}}\right)\right." border="0"/>

作者表示 $\boldsymbol{P}^{T}\left(\boldsymbol{I}_{S}-\frac{\boldsymbol{y}}{P \boldsymbol{x}^{n}+\boldsymbol{r}}\right)$ 是 $L(\boldsymbol{y} | \boldsymbol{x})$ 的梯度。

加入网络的正则项后的迭代式：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{x}^{n+1}=\left\{\boldsymbol{x}^{n}-\alpha^{n} \frac{\boldsymbol{x}^{n}}{\boldsymbol{P}^{T} \boldsymbol{I}_{S}}\left[\boldsymbol{P}^{T}\left(\boldsymbol{I}_{S}-\frac{\boldsymbol{y}}{\boldsymbol{P} \boldsymbol{x}^{n}+\boldsymbol{r}}\right)+f\left(\theta | \boldsymbol{x}^{n}\right)\right]\right\}_{+}" border="0"/>

网络结构图就很容易得出了：
<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200217152710.png"/>

### Bibtex:

```latex
@inproceedings{gong2019emnet,
  title={Emnet: an unrolled deep neural network for pet image reconstruction},
  author={Gong, Kuang and Wu, Dufan and Kim, Kyungsang and Yang, Jaewon and El Fakhri, Georges and Seo, Youngho and Li, Quanzheng},
  booktitle={Medical Imaging 2019: Physics of Medical Imaging},
  volume={10948},
  pages={1094853},
  year={2019},
  organization={International Society for Optics and Photonics}
}
```

