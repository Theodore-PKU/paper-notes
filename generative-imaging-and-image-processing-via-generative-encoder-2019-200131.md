### Paper:

Generative Imaging and Image Processing via Generative Encoder

### Author:

Lin Chen, Haizhao Yang

### Year:

2019

### Notes:

阅读时间：2020.01.31

泛读。这篇文章研究的是图像重建，可以说就是逆问题。作者的思路是结合自编码模型和 GAN。首先作者分析这两种方法的特点，作者指出，自编码模型（AE）可以有效地提取图像的低维信息，也就是低频的结构，但是很难捕捉细节，因此 AE 的输出一般很平滑。GAN 则对于细节呈现地很好，但是很容易在全局上出问题，这种问题一般就是低频结构出问题了。因此作者想结合两者的优点。作者提出的做法其实和 Bora 的做法非常类似，Bora  使用GAN 的方法是在测量值域上进行拟合，这篇文章则是把测量值域换成了编码器的编码。这篇文章中，作者使用的 GAN 是BEGAN，自编码网络使用了两种类型，不过都比较一般。

作者的训练方式如下：

1. 预训练一个 GAN
2. 用 GAN 生成的图像和真实的图像，一起来训练 AE
3. 选取 GAN 的生成器 $\mathcal{G}$ 和 AE 的 ecoder $\mathcal{EN}$
4. 给定真实图像或 corrupted 图像 x 的编码 $\mathcal{EN}(x)$，极小化下式（$S$ 取决于具体的问题）：

<img src="http://latex.codecogs.com/svg.latex? z^{*}=\arg \min _{z}\|\mathcal{E} \mathcal{N}(S(\mathcal{G}(z)))-m\|_{2}^{2}+\lambda\|z\|_{2}^{2}" border="0"/>

5. 最终的结果是 $\mathcal{G}(z^*)$

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-31%E4%B8%8B%E5%8D%889.23.32.png"/>

作者用这个方法和 Lasso，Bora 的方法比较，在比较压缩感知的时候，作者把编码器的输出当作是测量值。从实验效果上说，确实好非常多，但是这也很好理解，毕竟其他方法应该使用测量矩阵，而测量矩阵是不可能包含低频信息的提取。总的来说，作者提出的这个做法，是对 GAN 生成图像的某一种修正，也说明了低频信息在图像生成时应该是一个很重要的内容，它可以用来减少搜索空间。

### Bibtex:

```latex
@article{chen2019generative,
  title={Generative Imaging and Image Processing via Generative Encoder},
  author={Chen, Lin and Yang, Haizhao},
  journal={arXiv preprint arXiv:1905.13300},
  year={2019}
}
```

