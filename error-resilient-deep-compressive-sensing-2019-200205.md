### Paper:

Error Resilient Deep Compressive Sensing

### Author:

Thuong Nguyen Canh and Chien Trinh Van

### Year:

2019

### Notes:

阅读时间：2020.02.05

泛读。这篇文章研究的是压缩感知重建。着眼点在于测量值在传输过程中的损失。作者为此增加了一层网络来模拟这一过程（element-wise multiply a binary variable）。其他的框架和作者此前的 Multi-scale deep compressive sensing network 是一样的。总的来说，这篇文章没什么参考价值。

网络结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-05%E4%B8%8A%E5%8D%8811.16.36.png"/>

关于损失测量值层，在训练时，作者使用了两种方式，一种是固定测量值损失率，一种是在一定范围内变动。

### Bibtex:

```latex
@article{canh2019error,
  title={Error resilient deep compressive sensing},
  author={Canh, Nguyen and Van, Trinh and others},
  journal={arXiv preprint arXiv:1911.12507},
  year={2019}
}
```

