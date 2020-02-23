### Paper:

Pyramid Convolutional RNN for MRI Reconstruction

### Author:

Puyang Wang, Eric Z. Chen, Terrence Chen, Vishal M. Patel, Shanhui Sun

### Year:

2019

### Notes:

阅读时间：2020.01.23

精读。作者研究的是 MRI 的重建，主要的想法是利用不同 scale 的信息，不同的 scale 的重建都是使用一个 RNN 来实现。整体上，是由连续的三个 RNN 模块构成，每个模块按照顺序代表了不同的 scale，最开始的是最不精细的，最后一个是最精细的。作者是通过每个 ConvRNN 中的卷积的 stride 来控制。具体可以看论文的 table S1。每个 RNN 模块的输出最后一起输入到一个 CNN 中。需要注意的是，每个 RNN 的输入和输出都是 full-size，只是在 encoder 和 residual 部分，因为 stride 不为 1 的缘故，缩小了size。RNN 中有一个 DC 层来控制 data fidelity，具体做法就是保持原始的测量 kspace 系数不变，改变未采样的部分。

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{x_{1}=\operatorname{ConvRNN}_{1}\left(x_{0}, y, D, \theta_{1}\right)} \\ {x_{2}=\operatorname{ConvRNN}_{2}\left(x_{1}, y, D, \theta_{2}\right)} \\ {x_{3}=\operatorname{ConvRNN}_{3}\left(x_{2}, y, D, \theta_{3}\right)}\end{array}" border="0"/>

RNN的第 k+1 层的输出可以写成下面的形式 (这里的D表示的是采样的 scheme)：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} x_{i}^{(k+1)} &=D C\left(g_{i}\left(x_{i}^{(k)}, h_{i}^{(k)}, y, D_{i}, \theta_{i}\right)\right) \\ &=F^{-1}\left[D y+(1-D) F g_{i}^{d e c}\left(g_{i}^{r e s}\left(h_{i}^{(k)}\right)+g_{i}^{e n c}\left(x_{i}^{(k)}\right)\right)\right] \end{aligned}" border="0"/>

其中隐层是：

<img src="http://latex.codecogs.com/svg.latex? h_{i}^{(k)}=g_{i}^{r e s}\left(h_{i}^{(k-1)}\right)+g_{i}^{e n c}\left(x_{i}^{(k-1)}\right)" border="0"/>

事实上，隐层似乎就像一个 residual learning，见下图。

网络结构示意图（注意每个RNN其实等价于一个 某种结构的ResNet）：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-24%E4%B8%8A%E5%8D%8811.36.51.png"/>

虽然最终的效果看起来还不错，但是，从作者给出的中间结果，似乎第二个 RNN 模块的效果最好，而第三个模块并没有显示出细节，而且最终结果和第二个 RNN 的输出差不多。我认为在 pyramid 结构的设计上，还是可以继续改进的。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-24%E4%B8%8A%E5%8D%8811.50.20.png"/>

其他的一些细节：

1. 复数使用双通道。
2. multi-coil 就是将输入变成多个 coil 的结果，输出也是多个 coil，最后用 $\hat{x}_{\mathrm{rss}}=\sqrt{\sum_{c=1}^{n_{c}}\left|\hat{x}_{c}\right|^{2}}$ 来计算
3. loss 是 NMSE loss 和 SSIM loss
4. 学习率有一个 warmup 的过程，先用比较小的学习率，然后用比较大的学习率，最后再变小。

### Bibtex:

```latex
@article{wang2019pyramid,
  title={Pyramid Convolutional RNN for MRI Reconstruction},
  author={Wang, Puyang and Chen, Eric Z and Chen, Terrence and Patel, Vishal M and Sun, Shanhui},
  journal={arXiv preprint arXiv:1912.00543},
  year={2019}
}
```

