### Paper:

Accelerated Coronary MRI Using 3D SPIRIT-RAKI with Sparsity Regularization

### Author:

Seyed Amir Hossein Hosseini, Steen Moeller, Sebastian Weingärtner, Kâmil Uǧurbil and Mehmet Akçakaya

### Year:

2019

### Notes:

阅读日期：2020.02.28

泛读。这篇文章研究的是 MRI 的重建。因为是和冠状动脉相关，所以是 3D 的数据。作者的想法非常简单，类似于 RAKI 一样是对 kspace 的重建，但是作者希望设计的方法可以应用在任何 sampling pattern 之上。于是结合 SPIRiT 方法中的极小化 consistency 的方法提出了3D SPIRIT-RAKI，题目中说的 sparsity regularization 是指作者在最后的目标函数中加入了一个小波变换的正则项。具体而言，作者用一个 3D CNN 来实现对 kspace 的生成，用 ASC 数据训练好以后（因此这个方法是 scan-specific），对作者给出的目标函数进行极小化（使用的方法是 variable splitting，这里就加入了正则项，而且是图像域的，所以要把 kspace 转成 image）。但是这篇文章的 CNN 的训练没有说明清楚如何应用在任意 sampling pattern 上，只说了用 ASC 训练。从后面给出的目标函数的内容看，这个 CNN 更像是一个 kspace 去噪这样的效果。最近看了不少和 RAKI 方法有关的文章，感觉也没什么特别的。

CNN 的网络结构非常简单，只有四层。

<img src="https://cdn.mathpix.com/snip/images/iSVlWm9hYzG9jwAB2ApdIUnJM76rdDIA4LGfMWHmZXs.original.fullsize.png" width="70%"/>

训练完之后对下式的 $x$ 求极小（G 是 CNN，W 是小波变换，E 是转成 image）

<img src="http://latex.codecogs.com/svg.latex? \|\mathbf{y}-\mathbf{D} \mathbf{x}\|_{2}^{2}+\beta\|\mathbf{x}-\mathbf{G}(\mathbf{x})\|_{2}^{2}+\gamma\|\mathbf{W} \mathbf{E} \mathbf{x}\|_{1}" border="0"/>

### Bibtex:

```latex
@inproceedings{hosseini2019accelerated,
  title={Accelerated coronary MRI using 3D SPIRiT-RAKI with sparsity regularization},
  author={Hosseini, Seyed Amir Hossein and Moeller, Steen and Weing{\"a}rtner, Sebastian and U{\u{g}}urbil, K{\^a}mil and Ak{\c{c}}akaya, Mehmet},
  booktitle={2019 IEEE 16th International Symposium on Biomedical Imaging (ISBI 2019)},
  pages={1692--1695},
  year={2019},
  organization={IEEE}
}
```

