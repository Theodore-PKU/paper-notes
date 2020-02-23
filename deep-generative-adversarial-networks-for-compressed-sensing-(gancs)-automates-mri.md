### Paper:

Deep Generative Adversarial Networks for Compressed Sensing (GANCS) Automates MRI

### Author:

Morteza Mardani, Enhao Gong, Joseph Y. Cheng , Shreyas S. Vasanawala, Greg Zaharchuk, Lei Xing, and John M. Pauly

### Year:

2017

### Notes:

这篇文章的主要想法是使用一个resnet的网络作为生成器，图像域的loss是 1范数，还包括了kspace 的2范数损失函数。另外加上了  LSGAN 的对抗损失函数。作者认为 GAN 可以帮助生成更多纹理的细节。为了保证数据的 fidelity，作者还加入了一个模块 P（也就是将生成的图像的傅立叶高频信息填补到原来的为采样的地方）

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-26%E4%B8%8B%E5%8D%883.58.08.png" style="zoom:50%;" />

作者表示这个G + P_n的结构可以多次重复。也就是在卷积block里面可以插入P_n模块。

判别器loss：$\min _{\Theta_{d}} \mathbb{E}_{\mathbf{x}}\left[\left(1-\mathcal{D}\left(\mathbf{x} ; \mathbf{\Theta}_{d}\right)\right)^{2}\right]+\mathbb{E}_{\mathbf{y}}\left[\left(\mathcal{D}\left(\mathcal{G}\left(\mathbf{\Phi}^{\dagger} \mathbf{y} ; \mathbf{\Theta}_{g}\right) ; \mathbf{\Theta}_{d}\right)\right)^{2}\right]$

生成器loss：
$$
\begin{aligned}
\min _{\mathbf{\Theta}_{g}} \mathbb{E}_{\mathbf{y}}\left[\left\|\mathbf{y}-\mathbf{\Phi} \mathcal{G}\left(\mathbf{\Phi}^{\dagger} \mathbf{y} ; \mathbf{\Theta}_{g}\right)\right\|^{2}\right] &+\eta \mathbb{E}_{\mathbf{x}, \mathbf{y}}\left[\left\|\mathbf{x}-\mathcal{G}\left(\mathbf{\Phi}^{\dagger} \mathbf{y} ; \mathbf{\Theta}_{g}\right)\right\|_{1}\right] \\
&+\lambda \mathbb{E}_{\mathbf{y}}\left[\left(1-\mathcal{D}\left(\mathcal{G}\left(\mathbf{\Phi}^{\dagger} \mathbf{y} ; \mathbf{\Theta}_{g}\right) ; \mathbf{\Theta}_{d}\right)\right)^{2}\right]
\end{aligned}
$$
一些细节：1. 判别器的输出是取平均。

这篇文章后来发表了，[Deep Generative Adversarial Neural Networks for Compressive Sensing MRI](deep-generative-adversarial-neural-networks-for-compressed-sensing-mri.md)，修改了一些内容，增加了更多训练方面的细节。

### Bibtex:

```latex
@article{mardani2017deep,
  title={Deep generative adversarial networks for compressed sensing automates MRI},
  author={Mardani, Morteza and Gong, Enhao and Cheng, Joseph Y and Vasanawala, Shreyas and Zaharchuk, Greg and Alley, Marcus and Thakur, Neil and Han, Song and Dally, William and Pauly, John M and others},
  journal={arXiv preprint arXiv:1706.00051},
  year={2017}
}
```