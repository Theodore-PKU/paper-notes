### Paper:

Recurrent Generative Residual Networks for Proximal Learning and Automated Compressive Image Recovery

### Author:

Morteza Mardani, Hatef Monajemi, Vardan Papyan, Shreyas Vasanawala, David Donoho, and John Pauly

### Year:

2017

### Notes:

这篇文章的作者 Mardani 等人还有其他几篇文章，基本方法是一样的。参见[Deep Generative Adversarial Neural Networks for Compressive Sensing MRI](deep-generative-adversarial-neural-networks-for-compressed-sensing-mri.md), [Deep Generative Adversarial Networks for Compressed Sensing ( GANCS ) Automates MRI](deep-generative-adversarial-networks-for-compressed-sensing-(gancs)-automates-mri.md) 这两篇文章。这篇笔记里稍微回顾一下思路，以及对本文的特别之处进行说明。

作者的想法来源于传统的逆问题求解算法：
$$
\min _{\mathbf{x}}\|\mathbf{y}-\mathbf{\Phi} \mathbf{x}\|^{2}+\psi(\mathbf{x} ; \mathbf{\Theta})\\
\begin{aligned}
\mathbf{x}[k+1] &=\mathcal{P}_{\psi}\left\{\mathbf{x}[k]+\alpha \mathbf{\Phi}^{\mathrm{H}}(\mathbf{y}-\mathbf{\Phi} \mathbf{x}[k])\right\} \\
&=\mathcal{P}_{\psi}\left\{\alpha \mathbf{\Phi}^{\mathrm{H}} \mathbf{y}+\underbrace{\left(\mathbf{I}-\alpha \mathbf{\Phi}^{\mathrm{H}} \mathbf{\Phi}\right)}_{:=\mathbf{P}_{\mathcal{N}}} \mathbf{x}[k]\right\}
\end{aligned}
$$
在另外两篇文章中，作者提出的思路是用一个GAN的生成网络作为生成器，和 data fidelity 结合（用上面式子中的 $\mathcal{P}_{\psi}$，这篇文章有一个系数 $\alpha$），但是没有像传统算法一样有一个循环的结构。这篇文章仍然使用一样结构的GAN（残差结构），但整体是一个循环结构，所以损失函数也略有区别，生成器的 kspace loss 项包含了所有的中间生成项：
$$
\begin{aligned}
\min _{\Theta} \mathbb{E}_{\mathbf{y}}\left[\sum_{k=1}^{K}\left\|\mathbf{y}-\mathbf{\Phi} \check{\mathbf{x}}_{k}\right\|^{2}\right]+\lambda \mathbb{E}_{\mathbf{y}}\left[\left(1-\mathcal{D}\left(\hat{\mathbf{x}} ; \mathbf{\Theta}_{d}\right)\right)^{2}\right] \\
+\eta \mathbb{E}_{\mathbf{x}, \mathbf{y}}\left[\|\mathbf{x}-\hat{\mathbf{x}}\|_{1,2}\right]
\end{aligned}
$$
<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-28%E4%B8%8A%E5%8D%8811.58.11.png" style="zoom:50%;" />

此外，作者在实验中，也探究了生成网络共享参数和不共享参数的结果。

### Bibtex:

```latex
@article{mardani2017recurrent,
  title={Recurrent generative adversarial networks for proximal learning and automated compressive image recovery},
  author={Mardani, Morteza and Monajemi, Hatef and Papyan, Vardan and Vasanawala, Shreyas and Donoho, David and Pauly, John},
  journal={arXiv preprint arXiv:1711.10046},
  year={2017}
}
```



19800368193