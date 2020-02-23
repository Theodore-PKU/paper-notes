### Paper:

Alternating Phase Projected Gradient Descent with Generative Priors for Solving Compressive Phase Retrieval

### Author:

Rakib Hyder, Viraj Shah, Chinmay Hegde, and M. Salman Asif

### Year:

2019

### Notes:

阅读时间：2020.02.03

泛读。这篇文章研究的是 phase retrieval，使用的方法和 Bora 的框架一样，都是 GAN 作为 prior。只不过因为在 phase retrieval 问题中，测量值是对线性变换施加了绝对值运算，因此失去了 phase 信息。对此，作者在最简单的一阶梯度的算法之上做了一点改进，在迭代过程中增加一个 phase 的估计，使其变成一个线性的逆问题。不过总的来说没有太大区别，从实验结果上看，当测量值的数量比较多的时候，这些方法都没有差别。另外作者给出了一个收敛性的定理，这个定理本身比较容易理解，但是条件依旧苛刻，也就是必须满足真值 $x^*$ 属于生成器的值域。

最原始的算法求解：

<img src="http://latex.codecogs.com/svg.latex? \widehat{\mathbf{x}}=G\left(\underset{\mathbf{z}}{\arg \min }\|\mathbf{y}-|\mathbf{A} G(\mathbf{z})|\|^{2}\right)" border="0"/>

APGD 是：

<img src="http://latex.codecogs.com/svg.latex? \widehat{\mathbf{x}}_{t+1}=G\left(\arg \min _{\mathbf{z}}\left\|\mathbf{p}_{t} \odot \mathbf{y}-\mathbf{A} G(\mathbf{z})\right\|^{2}\right)" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \mathbf{p}_{t}=\text { phase }\left(\mathbf{A} \mathbf{x}_{t}\right):=\operatorname{sign}\left(\mathbf{A} \mathbf{x}_{t}\right)" border="0"/>

而 APPGD 则是：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-03%E4%B8%8B%E5%8D%883.21.16.png" width="60%"/>

### Bibtex:

```latex
@inproceedings{hyder2019alternating,
  title={Alternating phase projected gradient descent with generative priors for solving compressive phase retrieval},
  author={Hyder, Rakib and Shah, Viraj and Hegde, Chinmay and Asif, M Salman},
  booktitle={ICASSP 2019-2019 IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP)},
  pages={7705--7709},
  year={2019},
  organization={IEEE}
}
```

