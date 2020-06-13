### Notes on the number of linear regions of deep neural networks

Author: Guido Montúfar

Year: 2017

Published in: Sampling Theory Appl

```latex
@article{montufar2017notes,
  title={Notes on the number of linear regions of deep neural networks},
  author={Mont{\'u}far, Guido},
  journal={Sampling Theory Appl., Tallinn, Estonia, Tech. Rep},
  year={2017}
}
```

[Link](https://github.com/Theodore-PKU/paper-notes/blob/master/notes-on-the–number-of-linear-regions-of-deep-neural-networks.md)

Note: /Users/xieyutong/Documents/Research/PaperReading/Notes/notes-on-the–number-of-linear-regions-of-deep-neural-networks.md



这篇文章是之前的两篇文章的一个补充。主要的新的结论是关于网络分片数量的上界，作者给出了给更简单理解，但是不太紧的上界：
$$
\prod_{l=1}^{L} \sum_{j=0}^{m_{l-1}}\left(\begin{array}{c}
n_{l} \\
j
\end{array}\right), \quad m_{l-1}=\min \left\{n_{0}, \ldots, n_{l-1}\right\}
$$
这个上界显然比第2篇文章中的上界要好很多。