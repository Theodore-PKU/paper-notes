### Paper:

Eﬃcient Structurally-Strengthened Generative Adversarial Network for MRI Reconstruction

### Author:

Wenzhong Zhou, Huiqian Du, Wenbo Mei, Liping Fang

### Year:

2019

### Notes:

阅读时间：2020.02.02

泛读。这篇文章研究的是 MRI 重建，作者使用的方法是 GAN。这篇文章的主要创新点在于生成器网络的结构设计和损失函数。生成器的网络使用了两个 SAE，并且增加了很多 connection，以及使用了很多个 residual block，并且在 residual block 中继续添加了一层 residual learning，具体可以见示意图。损失函数方面，作者用了对抗loss，L1 loss，ssim/ms-ssim loss 和梯度loss。总的来说，没有什么新意。

输入是 zf-reconstrcution。示意图如下：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-02%E4%B8%8B%E5%8D%882.54.30.png"/>

损失函数：

<img src="http://latex.codecogs.com/svg.latex? L_{\text {total}}=L_{G A N}(G, D)+\alpha L_{1}(G)+\beta L_{E S}(G)" border="0"/>

其中：

<img src="http://latex.codecogs.com/svg.latex? L_{E S}=L_{M S^{-} S S I M}+L_{g r a d}" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? L_{M S^{-} S S I M}=1-M S-S S I M\left(x, x_{g}\right)" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? L_{g r a d}=\frac{1}{H W}\left\|\nabla x-\nabla x_{g}\right\|_{2}^{2}" border="0"/>

### Bibtex:

```latex
@article{zhou2019efficient,
  title={Efficient Structurally-Strengthened Generative Adversarial Network for MRI Reconstruction},
  author={Zhou, Wenzhong and Du, Huiqian and Mei, Wenbo and Fang, Liping},
  journal={arXiv preprint arXiv:1908.03858},
  year={2019}
}
```

