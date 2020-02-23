### Paper:

Deep Learning-Based Kronecker Compressive Imaging

### Author:

Thuong Nguyen Canh and Byeungwoo Jeon

### Year:

2018

### Notes:

泛读。这篇文章写的很短，实验结果也不出彩。使用的方法是基于 Kronecker Compressive Imaging 设计的深度学习重建。Kronecker CS 是在采样的时候使用两次 1xN 和 Nx1 卷积，其中需要一些重排，具体可以见图。初步重建是类似的相反过程。作者来论文中提到，训练方法是两阶段。第一阶段只训练sample和初步重建，第二阶段采样部分固定，只训练重建部分。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-03%E4%B8%8B%E5%8D%882.59.20.png" style="zoom: 50%;" />

### Bibtex:

```latex
@inproceedings{canh2018deep,
  title={Deep learning-based Kronecker compressive imaging},
  author={Canh, Thuong Nguyen and Jeon, Byeungwoo},
  booktitle={Proc. IEEE Inter. Conf. Consum. Elect. Asia},
  year={2018}
}
```

