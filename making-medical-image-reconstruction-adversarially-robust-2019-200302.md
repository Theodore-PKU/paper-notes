### Paper:

Making medical image reconstruction adversarially robust

### Author:

Adva Wolf

### Year:

2019

### Notes:

阅读日期：2020.03.02

泛读。严格意义上说，这篇文章应该是课程的论文，不过也有一些启发。这篇文章研究的是图像重建时的对抗攻击问题。作者借用分类问题中的鲁棒方法来增加医学图像（MRI）重建时的鲁棒性。思路也很简单，就是训练时对输入加一些高斯噪声，测试的时候，也对输入加高斯噪声，并且取多次的平均值作为最终的重建结果。实验表明，在损失一定精度（无对抗时）的情况下，网络的对抗性能增强很多。这里的攻击指的是在输入加某一有限制的扰动的情况下，让输出最偏离原始输出。网络结构是VN，不过这就和这篇文章没什么关系了。

测试时取平均：

<img src="http://latex.codecogs.com/svg.latex? f_{r s}\left(k_{s u b}\right)=\frac{1}{K} \sum_{k=1}^{K} f_{\theta_{n o i s e}}\left(k_{s u b}+\eta^{(k)}\right)" />

对抗攻击是极大化下式：

<img src="http://latex.codecogs.com/svg.latex? E(r)=\frac{1}{2}\left\|f\left(k_{s u b}+A_{S} r\right)-f\left(k_{s u b}\right)\right\|_{2}^{2}" />

### Bibtex:

```latex
@article{wolfmaking,
  title={Making medical image reconstruction adversarially robust},
  author={Wolf, Adva}
}
```

### Related work

作者引用的对抗攻击的文章：

1. Jeremy M Cohen, Elan Rosenfeld, and J Zico Kolter. Certiﬁed adversarial robustness via randomized smoothing. arXiv preprint arXiv:1902.02918, 2019.

2. Lecuyer, M., Atlidakis, V., Geambasu, R., Hsu, D., and Jana, S. Certiﬁed robustness to adversarial examples with diﬀerential privacy. In IEEE Symposium on Security and Privacy (SP), 2019.

### Code or link

None