### Paper:

Recurrent inference machines for reconstructing heterogeneous MRI data

### Author:

Kai Lønning, Patrick Putzky, Jan-Jakob Sonke, Liesbeth Reneman, Matthan W.A. Caan, Max Welling

### Year:

2019

### Notes:

泛读。这篇文章研究的是 MRI 的重建，采用的网络结构类似于 RNN，文章中称为 RIM，这篇文章虽然不是 unroll 形式的网络，但是也有 DC 的操作。具体来说，根据当前的 x，计算关于 DC 的梯度 dx，dx 和 x 都作为网络的输入，输出是 x 的新的更新方向。所谓的 RNN 是指，网络中有两个 GRU，每个 GRU 都有一个隐藏变量输出，这两个变量会参与到 GRU 的计算中，因此构成了类似 RNN 的结构。这两个量的实际含义没有分析。这篇文章比较新颖的地方在于网络的输入，把梯度作为网络的输入，和我的想法非常接近。

因为考虑的是多线圈的重建，所以梯度的计算复杂一点，sensitive matrix 是事先估计好的，损失函数也比较简单，是 MSE。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200325095554.png"/>

### Bibtex:

```latex
@article{lonning2019recurrent,
  title={Recurrent inference machines for reconstructing heterogeneous MRI data},
  author={L{\o}nning, Kai and Putzky, Patrick and Sonke, Jan-Jakob and Reneman, Liesbeth and Caan, Matthan WA and Welling, Max},
  journal={Medical image analysis},
  volume={53},
  pages={64--78},
  year={2019},
  publisher={Elsevier}
}
```

### 其他

