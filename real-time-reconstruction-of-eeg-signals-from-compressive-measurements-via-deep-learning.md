### Paper:

Real-time Reconstruction of EEG Signals from Compressive Measurements via Deep Learning

### Author:

Angshul Majumdar, Rabab Ward

### Year:

2016

### Notes:

这篇文章非常简单，先用测量矩阵的转置求出一个信号恢复的初始值，作为网络输入，然后用一个stacked 去噪自编码网络进行重建。SDAE的训练过程是逐层的。太过简单的做法了。

### Bibtex:

```latex
@inproceedings{majumdar2016real,
  title={Real-time reconstruction of EEG signals from compressive measurements via deep learning},
  author={Majumdar, Angshul and Ward, Rabab},
  booktitle={2016 International Joint Conference on Neural Networks (IJCNN)},
  pages={2856--2863},
  year={2016},
  organization={IEEE}
}
```