### Paper:

ADMM-Net for Robust Compressive Sensing Image Reconstruction in the Presence of Symmetric $α$-Stable Noise

### Author:

Yunyi Li, Liya Huang, Yue Yin, Yu Wang and Guan Gui

### Year:

2018

### Notes:

阅读日期：2020.01.15

泛读。这篇文章研究的是MRI的重建。不过和以往的文章不同的是，作者考虑的不是高斯噪声，而是 Symmetric $α$-stable (S$α$S) noise，作者在文章说这种噪声在信号度量时很常见的脉冲噪声。为了处理这种噪声，作者使用的是 1 范数而不是高斯噪声对应的 2 范数。作者的想法是将改动之后的目标函数用 ADMM 算法来求解，并且类似于 ADMM-net 的做法将其变成网络形式（整个转换形式基本一样的）。因为 1 范数的缘故，在求解 x 的子问题的时候，作者用了一个平滑项来替代 1 范数。

1 范数的目标函数：

<img src="http://latex.codecogs.com/svg.latex? \hat{\mathbf{x}} \leftarrow \arg \min _{\mathbf{x}}\|\mathbf{A} \mathbf{x}-\mathbf{y}\|_{1}+\lambda g(\mathbf{x})" border="0"/>

原子问题：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\mathbf{x}^{(n+1)}=\arg \min _{\mathbf{x}} \frac{1}{2}\|\mathbf{A} \mathbf{x}-\mathbf{y}\|_{1}} {-\sum_{l=1}^{L}\left\langle\alpha_{l}^{(n)}, \mathbf{z}_{l}^{(n)}-\mathbf{D}_{l} \mathbf{x}\right\rangle+\sum_{l=1}^{L} \frac{\rho_{l}}{2}\left\|\mathbf{z}_{l}^{(n)}-\mathbf{D}_{l} \mathbf{x}\right\|_{2}^{2}}\end{array}" border="0"/>

替代方式：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\frac{1}{2}\|\mathbf{A} \mathbf{x}-\mathbf{y}\|_{1}=\frac{1}{2}\|\mathbf{A} \mathbf{x}-\mathbf{y}\|_{1, \varepsilon}} {=\frac{1}{2}\|\mathbf{A} \tilde{\mathbf{x}}-\mathbf{y}\|_{1, \varepsilon}+\frac{1}{2}\langle\mathbf{A} \mathbf{x}-\mathbf{A} \tilde{\mathbf{x}}, d(\mathbf{A} \tilde{\mathbf{x}}-\mathbf{y})\rangle} {+\frac{1}{2 \tau}\|\mathbf{A} \mathbf{x}-\mathbf{A} \tilde{\mathbf{x}}\|_{2}^{2}}\end{array}" border="0"/>

其中：

<img src="http://latex.codecogs.com/svg.latex? \|\mathbf{A} \mathbf{x}-\mathbf{y}\|_{1, \varepsilon}=\sum_{i}\left[(\mathbf{A} \mathbf{x}-\mathbf{y})_{i}^{2}+\varepsilon^{2}\right]^{\frac{1}{2}}" border="0"/>

修改后的子问题有闭式解：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned}\mathbf{x}^{(n+1)} =\arg \min _{\mathbf{x}}+\frac{1}{2}\langle\mathbf{A} \mathbf{x}, d(\mathbf{A} \tilde{\mathbf{x}}-\mathbf{y})\rangle+\frac{1}{2 \tau}\|\mathbf{A} \mathbf{x}-\mathbf{A} \tilde{\mathbf{x}}\|_{2}^{2} -\sum_{l=1}^{L}\left\langle\alpha_{l}^{(n)}, \mathbf{z}_{l}^{(n)}-\mathbf{D}_{l} \mathbf{x}\right\rangle+\sum_{l=1}^{L} \frac{\rho_{l}}{2}\left\|z_{l}^{(n)}-\mathbf{D}_{l} \mathbf{x}\right\|_{2}^{2}\end{aligned}" border="0"/>

### Bibtex:

```latex
@inproceedings{li2018admm,
  title={ADMM-Net for Robust Compressive Sensing Image Reconstruction in the Presence of Symmetric $\alpha$-Stable Noise},
  author={Li, Yunyi and Huang, Liya and Yin, Yue and Wang, Yu and Gui, Guan},
  booktitle={Proceedings, APSIPA Annual Summit and Conference},
  volume={2018},
  pages={12--15},
  year={2018}
}
```

