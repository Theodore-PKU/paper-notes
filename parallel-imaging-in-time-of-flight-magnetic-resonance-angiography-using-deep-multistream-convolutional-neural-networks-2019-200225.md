### Paper:

Parallel imaging in time‐of‐flight magnetic resonance angiography using deep multistream convolutional neural networks

### Author:

Yohan Jun, Ho‐Joon Lee, Taejoon Eo, Hyungseob Shin, Taeseong Kim,  Dosik Hwang

### Year:

2019

### Notes:

阅读日期：2020.02.25

泛读。这篇文章研究的是并行 3d MRI 的重建。使用的方法是单纯的 CNN 网络。网络结构分成两个部分，一个是 MS-net，负责提取特征，因为有多个 channel，作者用了多个对应的子网络，输入就是每个 channel 的 zero-fill 的结果。MS-net 的输出则是每个子网络的输出的合并。另一个网络是 RC-net，负责用这些特征重建。该网络包含一个 residual learning，相当于获得一个更准确的不同 channel 的 image，然后再经过一个卷积层得到最终的 single channel 的图像。

损失函数：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\arg \min _{\theta}\left\|\mathbf{y}-D_{H}(\mathbf{x} ; \boldsymbol{\theta})\right\|_{2}^{2}} \\ {=\arg \min _{\boldsymbol{\theta}}\left\|\sqrt{\sum_{c=1}^{N_{c}}\left|\mathbf{I}^c_{f}\right|^{2}}-D_{H}\left(\left[\left|\mathbf{I}_{u}^{1}\right|,\left|\mathbf{l}_{u}^{2}\right|, \ldots,\left|\mathbf{I}_{u}^{V}\right|\right] ; \boldsymbol{\theta}\right)\right\|_{2}^{2}}\end{array}" border="0"/>

这里的 target 是 SSOS，$I^c_f$ 表示的是不同 channel 的 full 重建的结果（没有去掉 sensitive map）。$I_u^c$ 表示的是 zero-fill 的重建。 

网络结构图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200225165632.png" width="60%"/>

### Bibtex:

```latex
@article{jun2019parallel,
  title={Parallel imaging in time-of-flight magnetic resonance angiography using deep multistream convolutional neural networks},
  author={Jun, Yohan and Eo, Taejoon and Shin, Hyungseob and Kim, Taeseong and Lee, Ho-Joon and Hwang, Dosik},
  journal={Magnetic resonance in medicine},
  volume={81},
  number={6},
  pages={3840--3853},
  year={2019},
  publisher={Wiley Online Library}
}
```

