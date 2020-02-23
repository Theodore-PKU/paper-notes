### Paper:

Subject-Speciﬁc Convolutional Neural Networks for Accelerated Magnetic Resonance Imaging

### Author:

Mehmet Akçakaya, Steen Moeller, Sebastian Weingärtner and Kâmil Ugurbil

### Year:

2018

### Notes:

阅读日期：2020.02.10

泛读。这篇文章研究的并行MRI成像，作者的方法可以看成是 GRAPPA 算法的拓展。GRAPPA 算法概括起来，就是通过 ACS data 来训练出一个卷积核，用这个卷积核作用在其他的欠采样的 kspace lines 上，得到 kspace 的插值。这篇文章则是用一个 CNN 来代替这个卷积运算，因为是神经网络，所以有非线性，表达能力也更强。CNN 是一个三层网络，没有 bias（卷积的size也感觉比较奇怪）。作者采用的策略是，每个 coil 用一个 CNN，而输入都是所有的 coil 的 kspace，假设 coil 的数量是 $n_c$，加速程度为 $R$，那么网络输入的通道数是 $2n_c$，输出是 $R-1$，这里的 $R-1$，我认为值得是每 R lines 只有 one line 被采样了，所以需要重建出剩下的 R-1 lines，估计输出的 size 其中一个维度是 n/R（假设 n 表示 line 的总数）。训练时用的也是 ACS data，ACS 指的是 kspace 的低频部分，一般都是 full sample。我认为这里的训练，也有可能是只选取其中的 one line 当作sample，输入出是剩下的已知的 line。不过总的来说，GRAPPA 算法在直觉上很难理解其有效的原因。为何可以做到这么强的插值？

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200210170354.png"/>

### Bibtex:

```latex
@inproceedings{akccakaya2018subject,
  title={Subject-Specific Convolutional Neural Networks for Accelerated Magnetic Resonance Imaging},
  author={Ak{\c{c}}akaya, Mehmet and Moeller, Steen and Weing{\"a}rtner, Sebastian and U{\u{g}}urbil, K{\^a}mil},
  booktitle={2018 International Joint Conference on Neural Networks (IJCNN)},
  pages={1--6},
  year={2018},
  organization={IEEE}
}
```

