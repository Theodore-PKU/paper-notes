### Paper:

On instabilities of deep learning in image reconstruction - Does AI come at a cost?

### Author:

Vegard Antun, Francesco Renna, Clarice Poon, Ben Adcock, Anders C. Hansen

### Year:

2019

### Notes:

阅读时间：2020.02.01

精读。这篇文章研究的是深度学习图像重建算法的稳定性问题。作者提出了三种测试，来检查深度学习算法是否是稳定。三种测试：1. 小扰动，使得重建效果最差但是扰动的值尽量小的情况，这里的扰动是整幅图像的扰动；2. 在groudtruth 图像域加一些小标记和字母，作者使用的标记是黑桃、红桃等扑克牌的记号；3. 改变采样率，观察网络的效果的变化。作者的比较对象是传统的压缩感知算法（使用 GTV 和 shearlet transform），传统算法具体怎么计算的不是我的关注重点。实验结果概括起来就是，深度学习算法总是存在一些不稳定性，特别是小扰动，经常因为小扰动导致重建的结果变得很差（重建的时候是用带扰动的图像的测量值），对于标记和字母这些细节的结构变化，有的神经网络表现比较好，有的重建得也很差，对于改变采样样，有的神经网络表现正常，有的表现较差。而传统算法，都表现得很好，体现出非常强的稳定性。

测试的网络都比较有名：AUTOMAP、DAGAN、Deep MRI、MRI-VN、Ell 50、Med 50（应该是都看过的比较不错的文章）

这三个测试只有小扰动需要使用算法来计算，其它两个测试比较直接。小扰动的计算方法是用带动量的一阶梯度法极大化下式：

<img src="http://latex.codecogs.com/svg.latex? Q_{y}^{p}(r)=\frac{1}{2}\|f(y+A r)-p(x)\|_{2}^{2}-\frac{\lambda}{2}\|r\|_{2}^{2}" border="0"/>

这里的 $f$ 是网络，$y$ 是原始的测量值，$A$ 是测量矩阵，$r$ 是扰动，$p(x) = x$，$x$ 是原始图像。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-01%E4%B8%8A%E5%8D%8810.26.02.png"/>

下面给出一些结果。

小扰动：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-01%E4%B8%8A%E5%8D%8810.26.56.png"/>

对应的扰动：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-01%E4%B8%8A%E5%8D%8810.27.15.png"/>

小标记：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-01%E4%B8%8A%E5%8D%8810.28.45.png"/>

采样率变化：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-01%E4%B8%8A%E5%8D%8810.26.33.png"/>

> 这个不稳定的测试说明深度学习算法还有很多要改进的地方，特别是稳定性，我感觉和图像刻画直接相关。关于小扰动，作者没有给出有扰动情况下，测量值的情况，对于 MRI，难以想象小扰动的影响这么大。

### Bibtex:

```latex
@article{antun2019instabilities,
  title={On instabilities of deep learning in image reconstruction-Does AI come at a cost?},
  author={Antun, Vegard and Renna, Francesco and Poon, Clarice and Adcock, Ben and Hansen, Anders C},
  journal={arXiv preprint arXiv:1902.05300},
  year={2019}
}
```

