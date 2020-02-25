### Paper:

ETER-net: End to End MR Image Reconstruction Using Recurrent Neural Network

### Author:

Changheun Oh, Dongchan Kim, Jun-Young Chung, Yeji Han, and HyunWook Park

### Year:

2018

### Notes:

阅读日期：2020.02.25

泛读。这篇文章其实没什么参考价值，研究的是 MRI，思路类似于 AutoMap，不过用的网络是 RNN。作者想实现的并不是 undersample 的重建，而是单纯地建立 kspace 到 image 的映射，不过 kspace 可以不是笛卡尔采样。RNN 的设计上，主体是两个双向 RNN，输入分别是沿着 kspace 的行或者列。

<img src="https://cdn.mathpix.com/snip/images/2KScncmDbOcysuK9BQFryYiKQ9jmvckqvROe0tkcfMQ.original.fullsize.png" />

### Bibtex:

```latex
@inproceedings{oh2018eter,
  title={ETER-net: End to end MR image reconstruction using recurrent neural network},
  author={Oh, Changheun and Kim, Dongchan and Chung, Jun-Young and Han, Yeji and Park, HyunWook},
  booktitle={International Workshop on Machine Learning for Medical Image Reconstruction},
  pages={12--20},
  year={2018},
  organization={Springer}
}
```

