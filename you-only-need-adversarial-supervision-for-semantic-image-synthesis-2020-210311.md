# You Only Need Adversarial Supervision for Semantic Image Synthesis

[TOC]

## 信息

Authors: Vadim Sushko, Dan Zhang, Bernt Schiele, Edgar Schönfeld, Jürgen Gall, Anna Khoreva

Year: arXiv 2020

Bibtex:

```latex
@article{sushko2020you,
  title={You Only Need Adversarial Supervision for Semantic Image Synthesis},
  author={Sushko, Vadim and Sch{\"o}nfeld, Edgar and Zhang, Dan and Gall, Juergen and Schiele, Bernt and Khoreva, Anna},
  journal={arXiv preprint arXiv:2012.04781},
  year={2020}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/you-only-need-adversarial-supervision-for-semantic-image-synthesis.pdf)



## 概括

这篇文章虽然是 GAN 相关的文章，但并不完全是生成模型，而是一个conditional 的生成模型，任务是从一个分割区域的 map 出发生成图像。这篇文章的主要创新点在于设计了一个新的判别器的结构（和损失函数相关）。一般的判别器都只是判别真伪，但这篇文章用一个 U-Net 结构的分割网络作为判别器，判别的是分割之后的每个像素的类是否是 map 里的类。假设一共有 N 类，U-Net 分割的结果是 N+1 类，多出的一类表示 fake。这也就是说判别器对于真实图像，其分割结果是准确的，但是对于生成图像，希望判别的结果和真实的类接近，但同时要分辨出这是 fake 类。这种做法有点像同时拥有多个判别器一样。另外，这篇文章的生成器也和之前的其他方法不太一样（但我觉得更符合直觉）。这篇文章的生成器没有 encoder 结构，而是直接从 map 和 noise 出发生成，更接近原始的 GAN 模型的生成网络。而且 noise 是3D张量（包括通道了），所以在 2D 范围上还具有一定的控制性，而不是采用通常的一维向量作为隐变量。另外作者还增加了一个正则项，不过由于这篇文章对于自己的课题并无太多启发而不深究了。

![屏幕快照 2021-03-11 下午9.23.19](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-11 下午9.23.19.png)

