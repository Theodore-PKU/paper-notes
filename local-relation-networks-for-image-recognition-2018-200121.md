### Paper:

Local Relation Networks for Image Recognition

### Author:

Han Hu, Zheng Zhang, Zhenda Xie, Stephen Lin

### Year:

2018

### Notes:

泛读。这篇文章研究的是用一个新的线性层替代卷积网络。一般深度学习中使用的线性层都是卷积层，换言之，卷积是固定。但是这篇文章提出的线性层，其参数取决于图像本身。形式如下：

<img src="http://latex.codecogs.com/svg.latex? \omega\left(\mathbf{p}^{\prime}, \mathbf{p}\right)=\operatorname{softmax}\left(\Phi\left(f_{\theta_{q}}\left(\mathbf{x}_{\mathbf{p}^{\prime}}\right),f_{\theta_{k}}\left(\mathbf{x}_{\mathbf{p}}\right)\right)+f_{\theta_{g}}\left(\mathbf{p}-\mathbf{p}^{\prime}\right)\right)" border="0"/>

其中，$f_{\theta_g}(p - p')$ 表示的是相对位置，而 $f_{\theta_q}$ 和 $f_{\theta_k}$ 则是所谓的 query 和 key，不知道这些函数是怎么定义的。不过 $\Phi$ 作者给出了定义，可以是如下的几种形式：

- <img src="http://latex.codecogs.com/svg.latex? \Phi\left(q_{\mathbf{p}^{\prime}}, k_{\mathbf{p}}\right)=-\left(q_{\mathbf{p}^{\prime}}-k_{\mathbf{p}}\right)^{2}" border="0"/>
- <img src="http://latex.codecogs.com/svg.latex? \Phi\left(q_{\mathbf{p}^{\prime}}, k_{\mathbf{p}}\right)=-\left|q_{\mathbf{p}^{\prime}}-k_{\mathbf{p}}\right|" border="0"/>
- <img src="http://latex.codecogs.com/svg.latex? \Phi\left(q_{\mathbf{p}^{\prime}}, k_{\mathbf{p}}\right)=q_{\mathbf{p}^{\prime}} \cdot k_{\mathbf{p}}" border="0"/>

整体的线性层计算流程图如下：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-21%E4%B8%8B%E5%8D%884.33.30.png"/>

### Bibtex:

```latex
@inproceedings{hu2019local,
  title={Local relation networks for image recognition},
  author={Hu, Han and Zhang, Zheng and Xie, Zhenda and Lin, Stephen},
  booktitle={Proceedings of the IEEE International Conference on Computer Vision},
  pages={3464--3473},
  year={2019}
}
```

