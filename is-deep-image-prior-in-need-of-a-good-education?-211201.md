# Is Deep Image Prior in Need of a Good Education?

[TOC]

## 信息

作者：Riccardo Barbano, Johannes Leuschner, Maximilian Schmidt, Alexander Denker

年份：2021

期刊会议：arXiv

Bibtex：

```latex
@article{barbano2021deep,
  title={Is Deep Image Prior in Need of a Good Education?},
  author={Barbano, Riccardo and Leuschner, Johannes and Schmidt, Maximilian and Denker, Alexander and Hauptmann, Andreas and Maa{\ss}, Peter and Jin, Bangti},
  journal={arXiv preprint arXiv:2111.11926},
  year={2021}
}
```

code: educateddip.github.io/docs.educated_deep_image_prior/

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/is-deep-image-prior-in-need-of-a-good-education?.pdf)



## 概括

略读。

这篇文章使用了 DIP 的方法来解决 sparse view CT 重建的问题。作者考虑的是 DIP 作为一种无监督方法每次训练网络速度很慢，所以想到通过合成数据来训练网络，然后再使用 DIP 来 fine-tune。合成数据是椭圆这种形状在图片中处于不同位置，合成出 CT 的相关数据来训练。输入是 FBP 重建，MSE 的监督学习。当训练好之后，使用 DIP + TV 的损失函数作为无监督优化的目标函数，用 Adam 优化。论文里没有明确说 fine-tune 的时候，输入是随机变量还是 FBP 重建，但是我认为应该是 FBP 重建。从实验的内容来看，合成数据对于训练有效是可以理解的，因为重建的 CT 数据和合成数据有一定的相关性。没有看很多细节，比如网络结构有没有改变，是否是 Unet，怎么处理 early stop，完全由 TV 来解决或是采用了其他技巧。

我个人不喜欢这种方法，以后是不会跟进的。



## 方法

作者使用的 DIP 的框架包含了一个 TV 项，而不是单纯的 DIP，这相当于有两个正则项：
$$
\begin{aligned}
\theta_{t}^{*} \in \underset{\theta}{\operatorname{argmin}}\left\{l_{\mathrm{t}}(\theta):=\right.&\left.\left\|A \varphi_{\theta}(z)-y_{\delta}\right\|_{2}^{2}+\gamma \mathrm{TV}\left(\varphi_{\theta}(z)\right)\right\} \\
& x^{*}=\varphi_{\theta_{\mathrm{t}}^{*}}(z)
\end{aligned}
$$
从实验的数据来看，我认为这个方法有效的原因有两个：

1. 合成数据虽然是椭圆等图形，但是和数据集还是比较接近。
2. TV 项的影响应该比较大。



## 参考文献

这里有不少关于 DIP 的后续研究的论文（在 Related work 部分）。

有两篇是 CT 和 MRI 用 DIP 重建的方法。

Daniel Otero Baguer, Johannes Leuschner, and Maximilian Schmidt. Computed tomography reconstruction using deep image prior and learned reconstruction methods. Inverse Problems, 36(9):094004, 2020.

Mohammad Zalbagi Darestani and Reinhard Heckel. Accelerated MRI with un-trained neural networks. IEEE Trans. Computational Imaging, 7:724–733, 2021.