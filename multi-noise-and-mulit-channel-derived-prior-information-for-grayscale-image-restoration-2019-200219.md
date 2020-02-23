### Paper:

Multi-Noise and Multi-Channel Derived Prior Information for Grayscale Image Restoration

### Author:

Minghui Zhang, Yuan Yuan, Fengqin Zhang, Siyuan Wang, Shanshan Wang, Qiegen Liu

### Year:

2019

### Notes:

阅读日期：2020.02.19

泛读。这篇文章研究的是图像恢复，其实就是逆问题。作者采用的方法是去噪模型 + 迭代算法。去噪模型指的是正则项和去噪模型有关，具体而言是和正则项的导数相关。迭代算法则是 proximal gradient 算法，但是这里的 gradient 是正则项，proximal 是 data fidelity。题目中提到的 multi-noise，指的是去噪模型分成 3 个子网络，每个网络包含不同的噪声。而 multi-channel 指的是去噪模型中会对输入进行数据扩充，使用多个（3个）相同的通道来去噪，也可以理解为同时去噪三幅图像。去噪模型的基本骨架是 MWCNN，一个用了小波分解合成的 U-net 结构，然后包装成一个 dense-MWCNN。dense-MWCNN 用于 RGB 图像，如果是灰度图，不知道是不是就只用 MWCNN 了。multi-channel 的方法以前见过，不知道是不是也是作者的论文。这篇文章看看就好，创新的地方倒是不多。

下面从网络建构的角度一步一步给出相关的具体说明。

**MWCNN**，看图就行。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200219160415.png"/>

**dense-MWCNN** 是将 RGB 分成三个通道，每个通道和一个 MWCNN 相关。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200219160504.png"/>

整体的迭代算法

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{u^{k}=u^{k-1}-\operatorname{mean}\left[\nabla \operatorname{prior}_{L}^{s}\left(U^{k-1}\right)\right] / \rho} \\ {u^{k+1}=\arg \min _{u}\|H u-y\|^{2}+\gamma\left\|u-u^{k}\right\|^{2}}\end{array}\right." border="0"/>

注意，这里的第一个式子就是一个去噪模型，mean 指的是取平均，因为作者考虑了多个不同的噪声水平。具体见下式：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\nabla \text {prior}_{L}^{s}(U)} \\ {=\frac{1}{\sigma_{1}^{2}}\left(r_{\sigma_{1}} / \sqrt{2}\left(U+\eta_{1}\right)-U\right)} \\ {\quad+\frac{1}{\sigma_{2}^{2}}\left(r_{\sigma_{2}} / \sqrt{2}\left(U+\eta_{2}\right)-U\right)+\frac{1}{\sigma_{3}^{2}}\left(r_{\sigma_{3}} / \sqrt{2}\left(U+\eta_{3}\right)-U\right)}\end{array}" border="0"/>

训练的时候，去噪模型是单独训练的：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200219161009.png"/>

测试的时候则是要添加多个不同的噪声，最后取平均：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200219161050.png"/>

### Bibtex:

```latex
@article{zhang2019multi,
  title={Multi-Noise and Multi-Channel Derived Prior Information for Grayscale Image Restoration},
  author={Zhang, Minghui and Yuan, Yuan and Zhang, Fengqin and Wang, Siyuan and Wang, Shanshan and Liu, Qiegen},
  journal={IEEE Access},
  volume={7},
  pages={150082--150092},
  year={2019},
  publisher={IEEE}
}
```

