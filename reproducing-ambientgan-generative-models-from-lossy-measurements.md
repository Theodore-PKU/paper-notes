### Paper:

Reproducing AmbientGAN: Generative models from lossy measurements

### Author:

Mehdi Ahmadi, Timothy Nest, Mostafa Abdelnaim, Thanh-Dung Le

### Year:

2018

### Notes:

泛读。这篇文章是复现文章，复现的论文是 Ashish Bora, Eric Price, and Alexandros G Dimakis. Ambientgan: Generative models from lossy measurements. In International Conference on Learning Representations (ICLR), 2018.  这篇论文的主要想法是在训练GAN时，如果要生成的信号数据数量不够多，但是有测量数据（在各种逆问题意义下都可以），那么可以设计出相应的训练方式。

左图是传统的 GAN 训练。中间图的训练方式是先将测量值重建会原始信号再进行训练。右图是论文中提出的 AmbientGAN，判别器是在测量值域进行判断，作者的想法是如果测量值分布学到了，那么原始信号的分布也就学到了。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-03%E4%B8%8B%E5%8D%8810.36.36.png" style="zoom: 50%;" />

> 思考：这个想法和 Bora 的用 GAN 求解压缩感知一脉相承。但是原来的做法里因为 GAN 已经训练好了，理论 上可以避免零空间，但是这种 ambientGAN 理论上如何保持测量值分布和原始信号分布存在一一对应？假设不考虑网络本身的正则性，零空间应该无法避免才对。 

### Bibtex:

```latex
@article{ahmadi2018reproducing,
  title={Reproducing AmbientGAN: Generative models from lossy measurements},
  author={Ahmadi, Mehdi and Nest, Timothy and Abdelnaim, Mostafa and Le, Thanh-Dung},
  journal={arXiv preprint arXiv:1810.10108},
  year={2018}
}
```