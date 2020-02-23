### Paper:

Adaptive compressed sensing for wireless image sensor networks

### Author:

Junguo Zhang, Qiumin Xiang, Yaguang Yin, Chen Chen, Xin Luo

### Year:

2016

### Notes:

这篇文章是关于如何确定采样率的。问题背景是 无线传输情况下，需要用block压缩感知的方式来传递信息。压缩感知解决传输量的问题，block解决计算能力的问题。作者回顾了其他的adaptive算法的缺陷。最后作者提出的方法是先给定一个总体的测量数量，给定一个基本的每个block的测量数量。剩下的测量数量根据每个block的图像像素的标准差大小来分配。作者限制了每个block的采样率上限，如果有多出来的部分测量值数量指标，就匀给其他的所有block。用迭代的方式保证所有的block的测量数量低于上限，并且基本上标准差越大的block，采样率就更大。

作者在实验中探讨了block的size的影响。需要指出的是当block的size比较大的时候，标准差来体现图像的复杂度或者可压缩程度的性质变差，当block小的时候效果更好，但是当block数量过多，也会增加计算时间。在两者之间平衡，作者选取了 8x8 size的block。作者比较了这种方法和固定采样率的BCS方法，发现当总体采样率为0.1时，表现比较差，总体采样率比0.1高的时候效果好很多。我个人的理解是由于作者的基本采样数量的比例是固定的，如果总体采样率过低，这个基本采样数量可能太少了。因为不论总体采样率是多少，按照作者给出的分配算法，每个block得到采样指标的比例是不变的。如果基本的采样数量太少了，会使得整体效果变差。如果提高基本采样率，那么在总体采样率很少的情况下，应该效果也不会差太多。

### Bibtex:

```latex
@article{zhang2017adaptive,
  title={Adaptive compressed sensing for wireless image sensor networks},
  author={Zhang, Junguo and Xiang, Qiumin and Yin, Yaguang and Chen, Chen and Luo, Xin},
  journal={Multimedia Tools and Applications},
  volume={76},
  number={3},
  pages={4227--4242},
  year={2017},
  publisher={Springer}
}
```