### Paper:

Image Restoration from Patch-based Compressed Sensing Measurement

### Author:

Guangtao Nie, Ying Fu, Yinqiang Zheng, Hua Huang

### Year:

2019（17年已出来，19年发表）

### Notes:

阅读时间：2020.01.25

泛读。这篇文章研究的是 CS image 重建。因为17年就已经在 arxiv 上挂出来了，所以文章使用的方法很简单。作者考虑的 patch 的重建，首先对 patch 图像的测量值（如何测量论文里没说）用 FC 层+reshape 得到初步重建，再经过一个或多个 res block（示意图见下），得到重建结果。对于整幅图像，因为存在 blocky artifact，所以再通过一个 res block 进行 artifact removal。

整体网络结构：
<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-25%E4%B8%8A%E5%8D%8810.20.4.png"/>

res block 示意，使用的是三层卷积。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-25%E4%B8%8A%E5%8D%8810.20.5.png" width="50%"/>

关于实验，作者表示，对于较高的 MR，使用一个 res block 就可以了，对于较低的 MR，可以使用多个 res block。而在 artifact removal 部分，作者表示使用多个 res block 的效果不好...不知道为什么。

### Bibtex:

```latex
@article{huang2019image,
  title={Image restoration from patch-based compressed sensing measurement},
  author={Huang, Hua and Nie, Guangtao and Zheng, Yinqiang and Fu, Ying},
  journal={Neurocomputing},
  volume={340},
  pages={145--157},
  year={2019},
  publisher={Elsevier}
}
```

