### Paper:

Low Shot Learning with Untrained Neural Networks for Imaging Inverse Problems

### Author:

Oscar Leong, Wesam Sakla

### Year:

2019

### Notes:

阅读时间：2020.02.05

泛读。这篇文章研究的是使用极少量数据和 deep network prior 求解图像逆问题。一般来说，DIP 不需要使用训练数据，对于 GAN 的方法，需要大量数据。这两个方法的优化对象是不同的。作者提出的方法，则介于两者之间。假设此时只有少量数据 (5~100)，先对网络参数和 latent space 进行训练，使 loss 最小。这是预训练的过程。在重建时，先对 latent space 进行优化，就像 GAN 方法一样。当得到一个合适的 latent variable 时，同时对网络参数和 latent space 进行优化，得到最终结果。latent space 的初始化时高斯分布。损失函数方面，在预训练时作者使用了两种损失函数，一个是图像域的 2 范数，一个是带 kernel 的损失函数，在重建时，使用的损失函数都是测量值的 2 范数。

kernel loss：

<img src="http://latex.codecogs.com/svg.latex? \min _{\boldsymbol{\theta}, \boldsymbol{z}_{1}, \ldots, \boldsymbol{z}_{S}} \frac{1}{\left(\begin{array}{l}{S} \\ {2}\end{array}\right)} \sum_{i \neq i^{\prime}} k\left(\mathcal{G}\left(\boldsymbol{z}_{i} ; \boldsymbol{\theta}\right), \mathcal{G}\left(\boldsymbol{z}_{i} ; \boldsymbol{\theta}\right)\right)+\frac{1}{\left(\begin{array}{c}{S} \\ {2}\end{array}\right)} \sum_{j \neq j^{\prime}} k\left(\boldsymbol{x}_{j}, \boldsymbol{x}_{j^{\prime}}\right)-\frac{2}{\left(\begin{array}{c}{S} \\ {2}\end{array}\right)} k\left(\mathcal{G}\left(\boldsymbol{z}_{i} ; \boldsymbol{\theta}\right), \boldsymbol{x}_{j}\right)" border="0"/>

实验的结果，GAN 表现的不怎么样，DIP 在测量值较多的时候表现的不比 5-shot 差：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-05%E4%B8%8B%E5%8D%881.12.52.png"/>

### Bibtex:

```latex
@article{leong2019low,
  title={Low Shot Learning with Untrained Neural Networks for Imaging Inverse Problems},
  author={Leong, Oscar and Sakla, Wesam},
  journal={arXiv preprint arXiv:1910.10797},
  year={2019}
}
```

