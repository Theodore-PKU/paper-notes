### Paper:

Adversarial Regularizers in Inverse Problems

### Author:

Sebastian Lunz, Ozan Öktem, Carola-Bibiane Schönlieb

### Year:

2018

### Notes:

阅读日期：2020.03.08

精读。这篇文章研究的是逆问题的重建，实验的例子是去噪和 CT。作者的方法是先训练一个正则项，然后再用一阶梯度法求解变分问题。这个正则项类似于 GAN 的判别器，是用来区分 corrupted image 和 groundtruth（个人理解，也可以看成是先验概率的一种估计）。作者给出了一些理论分析，证明这个判别器就是计算 x 到 image prior 的流形的距离（这个定理很重要，可以参考）。这个距离从某种意义上说，也可以看成是概率。文章中没有提判别器的网络结构，在去噪的实验中，说是无监督的，但是也没有提怎么无监督训练判别器（事实上，判别器的训练的算法里用到了 groundtruth）

判别器的训练损失函数：
$$
\mathbb{E}_{X \sim \mathbb{P}_{r}}\left[\Psi_{\Theta}(X)\right]-\mathbb{E}_{X \sim \mathbb{P}_{n}}\left[\Psi_{\Theta}(X)\right]+\lambda \cdot \mathbb{E}\left[\left(\left\|\nabla_{x} \Psi_{\Theta}(X)\right\|-1\right)_{+}^{2}\right]
$$
这里的 $\Psi$ 指的是网络，Pr 是 groundtruth，Pn 是 corrupted image。最后一项是一个李普西次条件的约束。

训练判别器的算法：

![image-20200308202418507](/Users/xieyutong/Library/Application Support/typora-user-images/image-20200308202418507.png)

训练完判别器后，变分模型求解的算法：

![image-20200308202624818](/Users/xieyutong/Library/Application Support/typora-user-images/image-20200308202624818.png)

作者还给出了 $\lambda$ 的一个估计公式（e 是噪声）：
$$
\lambda=2 \mathbb{E}_{e \sim p_{n}}\left\|A^{*} e\right\|_{2}
$$

### Bibtex:

```latex
@inproceedings{lunz2018adversarial,
  title={Adversarial regularizers in inverse problems},
  author={Lunz, Sebastian and {\"O}ktem, Ozan and Sch{\"o}nlieb, Carola-Bibiane},
  booktitle={Advances in Neural Information Processing Systems},
  pages={8507--8516},
  year={2018}
}
```

