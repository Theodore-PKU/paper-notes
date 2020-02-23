### Paper:

VS-Net: Variable splitting network for accelerated parallel MRI reconstruction

### Author:

Jinming Duan, Jo Schlemper, Chen Qin, Cheng Ouyang, Wenjia Bai, Carlo Biﬃ, Ghalib Bello, Ben Statton, Declan P O’Regan, Daniel Rueckert

### Year:

2019

### Notes:

阅读日期：2020.02.22

泛读。这篇文章研究的是 parallel mri 的重建，用的方法仍然是展开一个传统算法。类似于 ADMM，作者用的是 variable splitting，区别在于没有拉格朗日乘子的部分，相对更简单一些。和正则项有关的那一个子问题的计算用一个去噪 CNN 替代了，作者表示，可以结合去噪网络的进展来选择更合适的网络。网络中每个 stage 分成三个部分，去噪模块 DB，data consistency 模块 DCB，合成模块，因为是多线圈，所以会增加这个合成模块。损失函数是普通的 2 范数。

原始目标函数：

<img src="http://latex.codecogs.com/svg.latex? \min _{m, u, x_{i}} \frac{\lambda}{2} \sum_{i=1}^{n_{c}}\left\|\mathcal{D} \mathcal{F} x_{i}-y_{i}\right\|_{2}^{2}+\mathcal{R}(u) \text { s.t. } m=u, S_{i} m=x_{i}, \forall i \in\left\{1,2, \ldots, n_{c}\right\}" border="0"/>

写成无约束的问题：

<img src="http://latex.codecogs.com/svg.latex? \min _{m, u, x_{i}} \frac{\lambda}{2} \sum_{i=1}^{n_{c}}\left\|\mathcal{D} \mathcal{F} x_{i}-y_{i}\right\|_{2}^{2}+\mathcal{R}(u)+\frac{\alpha}{2} \sum_{i=1}^{n_{c}}\left\|x_{i}-S_{i} m\right\|_{2}^{2}+\frac{\beta}{2}\|u-m\|_{2}^{2}" border="0"/>

对应的 vs 算法：

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{u^{k+1}=\arg \min _{a} \frac{\beta}{2}\left\|u-m^{k}\right\|_{2}^{2}+\mathcal{R}(u)} \\ {x_{i}^{k+1}=\arg \min _{x_{i}} \lambda \sum_{i=1}^{n_{c}}\left\|\mathcal{D} \mathcal{F} x_{i}-y_{i}\right\|_{2}^{2}+\frac{\alpha}{2} \sum_{i=1}^{n_{c}}\left\|x_{i}-S_{i} m^{k}\right\|_{2}^{2}} \\ {m^{k+1}=\arg \min _{m} \frac{\alpha}{2} \sum_{i=1}^{n_{c}}\left\|x_{i}^{k+1}-S_{i} m\right\|_{2}^{2}+\frac{\beta}{2}\left\|u^{k+1}-m\right\|_{2}^{2}}\end{array}\right." border="0"/>

由此得到对应的网络模块计算：

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{u^{k+1}=\text { denoiser }\left(m^{k}\right)} \\ {x_{i}^{k+1}=\mathcal{F}^{-1}\left(\left(\lambda \mathcal{D}^{T} \mathcal{D}+\alpha I\right)^{-1}\left(\alpha \mathcal{F} S_{i} m^{k}+\lambda \mathcal{D}^{T} y_{i}\right)\right) \quad \forall i \in\left\{1,2, \ldots, n_{c}\right\}} \\ {m^{k+1}=\left(\beta I+\alpha \sum_{i=1}^{n_{c}} S_{i}^{H} S_{i}\right)^{-1}\left(\beta u^{k+1}+\alpha \sum_{i=1}^{n_{c}} S_{i}^{H} x_{i}^{k+1}\right)}\end{array}\right." border="0"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200222154354.png"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200222154317.png"/>

### Bibtex:

```latex
@inproceedings{duan2019vs,
  title={VS-Net: Variable splitting network for accelerated parallel MRI reconstruction},
  author={Duan, Jinming and Schlemper, Jo and Qin, Chen and Ouyang, Cheng and Bai, Wenjia and Biffi, Carlo and Bello, Ghalib and Statton, Ben and O’Regan, Declan P and Rueckert, Daniel},
  booktitle={International Conference on Medical Image Computing and Computer-Assisted Intervention},
  pages={713--722},
  year={2019},
  organization={Springer}
}
```

