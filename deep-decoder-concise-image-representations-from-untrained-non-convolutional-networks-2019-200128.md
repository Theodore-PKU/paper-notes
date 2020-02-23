### Paper:

Deep Decoder: Concise Image Representations from Untrained Non-convolutional Networks

### Author:

Reinhard Heckel and Paul Hand

### Year:

2019

### Notes:

阅读时间：2020.01.28

泛读。这篇文章此前看过了，主要的内容是和 DIP 一样的，主要的区别在于使用的网络结构不同。这篇文章用的是 1x1 卷积 + 上采样等。作者使用的网络的参数比较少，所以也可以用于图像压缩（实验结果表明，当压缩率较高时 deep decoder 的效果好一些，当压缩率较低时小波压缩更好一点）。在实验方面，作者做了去噪、超分辨等实验。特别是，作者比较了 DIP 和 deep decoder 在拟合噪声的表现，实验表明 deep decoder 更不容易拟合噪声。

网络结构

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-28%E4%B8%8B%E5%8D%889.58.22.png" width="50%"/>

图像压缩的效果

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-28%E4%B8%8B%E5%8D%889.58.22.png" width="50%"/>

拟合噪声的结果

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-28%E4%B8%8B%E5%8D%889.58.36.png" width="70%"/>

### Bibtex:

```latex
@article{heckel2018deep,
  title={Deep decoder: Concise image representations from untrained non-convolutional networks},
  author={Heckel, Reinhard and Hand, Paul},
  journal={arXiv preprint arXiv:1810.03982},
  year={2018}
}
```

