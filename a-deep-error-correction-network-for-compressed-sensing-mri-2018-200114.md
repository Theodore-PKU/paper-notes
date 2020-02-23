### Paper:

A Deep Error Correction Network for Compressed Sensing MRI

### Author:

Liyan Sun, Zhiwen Fan, Yue Huang, Xinghao Ding, John Paisley

### Year:

2018

### Notes:

阅读日期：2020.01.14

泛读。这篇文章解决的是MRI重建的问题，作者的思考点在于设计了一个网络来估计现有的重建算法的误差。总的框架包含三个部分，第一个部分是某一重建算法，得到一个guidance结果 $\bar{x}_p$，第二个部分是误差估计网络，输入是guidance结果和 zero-fill 重建的结果，输出是 error 估计 $\Delta x_p$，最终得到 $x_p= \bar{x}+p + \Delta_p$，第三个部分是data fidelity 项。

网络框架：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-14%E4%B8%8B%E5%8D%884.16.04.png"/>

data fidelity 项和一般的方法中提到的没有什么区别。

<img src="http://latex.codecogs.com/svg.latex? \hat{x}=\arg \min \left\|F_{u} x-y\right\|_{2}^{2}+\alpha\left\|x-\left(\bar{x}_{p}+f_{\theta}\left(\mathcal{Z}(y), \bar{x}_{p}\right)\right)\right\|_{2}^{2}"  border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \hat{x}=F^{H} \frac{F_{u}^{H} y+\alpha F\left(\bar{x}_{p}+f_{\theta}\left(\mathcal{Z}(y), \bar{x}_{p}\right)\right)}{F F_{u}^{H} F_{u} F^{H}+\alpha I}" border="0"/>

这篇文章最大的问题在于假设使用了 Deep Learning 的方法，用了残差学习，那么这个不非常类似于残差的残差，或者说就是 cascade 的残差结构就能实现相同的效果。因此个人觉得没有什么参考价值。

### Bibtex:

```latex
@article{sun2018deep,
  title={A Deep Error Correction Network for Compressed Sensing MRI},
  author={Sun, Liyan and Fan, Zhiwen and Huang, Yue and Ding, Xinghao and Paisley, John},
  journal={arXiv preprint arXiv:1803.08763},
  year={2018}
}
```

