### Paper:

Low-Dose CT with Deep Learning Regularization via Proximal Forward Backward Splitting

### Author:

Qiaoqiao Ding, Gaoyu Chen, Xiaoqun Zhang, Qiu Huang, Hui Ji and Hao Gao

### Year:

2019

### Notes:

阅读日期：2020.02.17

泛读。这篇文章研究的是 Low-dose CT 重建，用的方法是 unroll 一个 proximal forward backward splitting 算法，其实就是 ISTA。还是老套路，proximal 算子用一个 CNN 替代了，gradien discend 那一步的步长可学习。不过还有两个小细节的变化。一个是 gradient 计算使用了 A 的伪逆而不是转置，一个是网络 CNN 的输入不只是 gradient 的结果，还有此前所有的 gradient 步的中间值，合并起来作为输入。感觉没啥参考价值。

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{\boldsymbol{x}^{k+\frac{1}{2}}=\Lambda_{\theta_{k}^{1}}\left(\boldsymbol{x}^{k}, \boldsymbol{A}^{+}\left(\boldsymbol{A} \boldsymbol{x}^{k}-\boldsymbol{y}\right)\right)} \\ {\boldsymbol{x}^{k+1}=\Lambda_{\theta_{k}^{2}}\left(\boldsymbol{x}^{k+\frac{1}{2}}, \boldsymbol{x}_{k}\right)}\end{array}\right." border="0"/>

第一步其实还是：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{x}^{k+\frac{1}{2}}=\boldsymbol{x}^{k}-\alpha \boldsymbol{A}^{+}\left(\boldsymbol{A} \boldsymbol{x}^{k}-\boldsymbol{y}\right)" border="0"/>

第二步的 $x_k$ 是 $\left(\boldsymbol{x}^{\frac{1}{2}}, \boldsymbol{x}^{1+\frac{1}{2}}, \cdots \boldsymbol{x}^{k-\frac{1}{2}}\right)$

网络结构其实很简单：
<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200217162643.png"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200217162652.png" width="60%"/>

### Bibtex:

```latex
@article{ding2019low,
  title={Low-Dose CT with Deep Learning Regularization via Proximal Forward Backward Splitting},
  author={Ding, Qiaoqiao and Chen, Gaoyu and Zhang, Xiaoqun and Huang, Qiu and Gao, Hui Jiand Hao},
  journal={arXiv preprint arXiv:1909.09773},
  year={2019}
}
```

