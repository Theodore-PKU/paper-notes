### Paper:

A Prior Learning Network for Joint Image and Sensitivity Estimation in Parallel MR Imaging

### Author:

Nan Meng, Yan Yang, Zongben Xu, and Jian Sun

### Year:

2019

### Notes:

阅读日期：2020.02.29

泛读。这篇文章研究的并行MRI的重建，作者的想法是展开迭代算法，将其中的 proximal 算子用网络表示。想法简单，作者的创新之处将 sensitivity 的估计也放入网络中，由网络来训练，具体的做法就是在目标函数中加入一项关于 sensitivity 的 prior 项（因此目标函数有两个 prior，一个 data consistency，一个是图像和 sensitive image 之间的 consistency）。这样就需要对 sensitivity 也进行优化。虽然目标函数包含 prior，但是这个 prior 对应的 proximal 算子最后被网络替代了，所以事先我们也没有对 prior 有什么限制，而且损失函数也包含 sensitivity，相当于是作者让网络自己自己估计出 sensitivity。关于 S 和 U（前者是 sensitivity，后者是图像）的两个子问题的求解，用的是迭代算法，所以对应的网络是 cascade 的形式。网络的细节部分可以参考论文，比较简单。

目标函数：

<img src="http://latex.codecogs.com/svg.latex? E(\boldsymbol{V}, \boldsymbol{S}, U)=\frac{1}{2} \sum_{l}\left\|M \mathcal{F} V_{l}-y_{l}\right\|_{2}^{2}+\frac{\rho}{2} \sum_{l}\left\|S_{l} \odot U-V_{l}\right\|_{2}^{2}+\beta \sum_{l} R\left(S_{l}\right)+\lambda P(U)" />

迭代算法包含三步

<img src="http://latex.codecogs.com/svg.latex? V_{l}^{(n)}=\mathcal{F}^{H} \Lambda^{-1}\left(M^{H} y_{l}+\rho \mathcal{F}\left(S_{l}^{(n-1)} \odot U^{(n-1)}\right)\right)" />

<img src="http://latex.codecogs.com/svg.latex? S_{l}^{(n, k)}=\operatorname{Prox}_{\frac{\beta}{\alpha \rho}} R\left(\frac{\left(U^{(n-1)}\right)^{*} \odot V_{l}^{(n)}+\alpha S_{l}^{(n, k-1)}}{\left(U^{(n-1)}\right)^{*} \odot U^{(n-1)}+\alpha}\right), k=1, \cdots, K" />

<img src="http://latex.codecogs.com/svg.latex? U^{(n, k)}=\operatorname{Prox}_{\frac{\lambda}{\rho \gamma} P}\left(\frac{\sum_{l}\left(S_{l}^{(n)}\right)^{*} \odot V_{l}^{(n)}+\gamma U^{(n, k-1)}}{\sum_{l}\left(S_{l}^{(n)}\right)^{*} \odot S_{l}^{(n)}+\gamma}\right), k=1, \cdots, K" />

对应展开的网络结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200301165221.png"/>

U，S两个子网络的形式都是 cascade 的CNN，因为是一个循环迭代。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200301165207.png"/>

损失函数包含好几个和 sensitivity 有关的项：

<img src="http://latex.codecogs.com/svg.latex? \mathcal{L}(\hat{\boldsymbol{S}}, \hat{U})=\sum_{l}\left\|\hat{S}_{l}-S_{l}^{g t}\right\|_{1}+\sum_{l}\left\|\left|\hat{S}_{l}\right|-\left|S_{l}^{g t}\right|\right\|_{1}+\sum_{l} \mathrm{TV}\left(\left|\hat{S}_{l}\right|\right)+\xi\left\|\hat{U}-U^{g t}\right\|_{2}^{2}" />

### Bibtex:

```latex
@inproceedings{meng2019prior,
  title={A Prior Learning Network for Joint Image and Sensitivity Estimation in Parallel MR Imaging},
  author={Meng, Nan and Yang, Yan and Xu, Zongben and Sun, Jian},
  booktitle={International Conference on Medical Image Computing and Computer-Assisted Intervention},
  pages={732--740},
  year={2019},
  organization={Springer}
}
```

