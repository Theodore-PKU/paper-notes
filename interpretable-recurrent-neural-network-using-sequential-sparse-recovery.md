### Paper:

Interpretable Recurrent Neural Networks Using Sequential Sparse Recovery

### Author:

Scott Wisdom, Thomas Powers, James Pitton, and Les Atlas

### Year:

2016

### Notes:

这篇文章作者关注的是如何让网络参数更有解释性。作者研究的对象是具有序列关系的信号恢复问题。文章中对序列信号有一个线性的关系假设，非常强。RNN的结构和ISTA算法非常类似，思路也是unfolding。但是最终的效果其实没多好。另外作者在文章中关于网络训练的内容写得不详细，不清楚具体如何训练。

一些论文的细节内容。

作者的模型假设：
$$
\mathbf{y}_{t}=\mathbf{F} \mathbf{y}_{t-1}+\mathbf{v}_{t}\\
\mathbf{x}_{t} \sim \mathcal{N}\left(\mathbf{A} \mathbf{D} \mathbf{h}_{t}, \sigma^{2} \mathbf{I}\right)\\
p\left(\mathbf{h}_{t} | \mathbf{h}_{t-1}\right) \propto \exp \left\{-\nu_{1}\left\|\mathbf{h}_{t}\right\|_{1}-\frac{\nu_{2}}{2}\left\|\mathbf{D} \mathbf{h}_{t}-\mathbf{F} \mathbf{D} \mathbf{h}_{t-1}\right\|_{2}^{2}\right\}
$$
其中 $h$ 是稀疏隐变量，$x$ 是测量值，$A$ 是测量矩阵，$D$ 是字典，$y$ 是信号，$F$ 是信号之间的关系。从给出的算法上看，基本上是按照 ISTA 算法的思路构建的。网络结构是RNN类型。下图的左边是传统RNN，右边是作者提出的结构。作者还建立了两个网络之间的参数对应关系。

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-19%E4%B8%8A%E5%8D%8810.55.59.png)

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-19%E4%B8%8A%E5%8D%8810.56.05.png)

### Bibtex:

```latex
@article{wisdom2016interpretable,
  title={Interpretable recurrent neural networks using sequential sparse recovery},
  author={Wisdom, Scott and Powers, Thomas and Pitton, James and Atlas, Les},
  journal={arXiv preprint arXiv:1611.07252},
  year={2016}
}
```