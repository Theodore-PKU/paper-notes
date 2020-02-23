### Paper:

Robust Compressive Phase Retrieval via Deep Generative Priors

### Author:

Fahad Shamshad, Ali Ahmed

### Year:

2018

### Notes:

阅读日期：2020.01.11

泛读。这一篇用的是 Bora 的方法，用GAN解 CPR 问题。CPR 问题不是线性系统，在线性系统外加了绝对值。从作者的实验结果上看，当测量值较多的时候，这种方法表现就不那么好了，但是测量值很少的时候，这种方法有很大的优势。另外使用这种方法，不一定是GAN生成的图像中最接近真实图像的（目标函数就不是测量值的比较，而是图像域loss），因为这涉及到零空间的问题。零空间问题是这种解法最值得思考的。为了避免局部最优，作者的处理是选择多个latent z 的初值进行 gradient 迭代，保留loss最小的。

目标函数：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{z}^{*}=\underset{\boldsymbol{z} \in \mathbb{R}^{k}}{\operatorname{argmin}}\|\boldsymbol{y}-| \boldsymbol{A} G(\boldsymbol{z})\|^{2}"  border="0"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-11%E4%B8%8B%E5%8D%887.44.39.png"/>

### Bibtex:

```latex
@article{shamshad2018robust,
  title={Robust compressive phase retrieval via deep generative priors},
  author={Shamshad, Fahad and Ahmed, Ali},
  journal={arXiv preprint arXiv:1808.05854},
  year={2018}
}
```

