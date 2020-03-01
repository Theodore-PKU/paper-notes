### Paper:

Deep Learning Enables Reduced Gadolinium Dose for Contrast-Enhanced Brain MRI

### Author:

Enhao Gong, John M. Pauly, Max Wintermark, and Greg Zaharchuk

### Year:

2018

### Notes:

阅读日期：2020.02.29

泛读。这篇文章研究的是 enhance contrast 的 MRI，这里借助了一种元素钆。作者的目的是降低该元素的剂量，但是保持原来的 contrast 的效果，因此类似于 low-dose CT。作者的方法特别简单，就是一个 U-net 结构，局部的block有 residual connection。网络的 input 是 0-dose 和 10%-dose 的图像，输出是 full-dose 图像。没什么参考价值的文章。

附上一个网络结构示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200301195523.png"/>

### Bibtex:

```latex
@article{gong2018deep,
  title={Deep learning enables reduced gadolinium dose for contrast-enhanced brain MRI},
  author={Gong, Enhao and Pauly, John M and Wintermark, Max and Zaharchuk, Greg},
  journal={Journal of Magnetic Resonance Imaging},
  volume={48},
  number={2},
  pages={330--340},
  year={2018},
  publisher={Wiley Online Library}
}
```

