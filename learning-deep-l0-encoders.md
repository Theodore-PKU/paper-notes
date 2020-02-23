### Paper:

Learning Deep  $l_0$ Encoders

### Author:

Zhangyang Wang, Qing Ling, Thomas S. Huang

### Year:

2016

### Notes:

这篇文章同样是将传统算法展开为神经网络的想法。不过传统算法解决的是 0 范数的问题，不是 1范数。原始的问题描述是将信号用字典表示，系数是稀疏的，因此没有设计到压缩这一部分。对于0范数的问题，有两种模型，一种是作为正则项，一种是限制系数的个数为M。解正则项的传统算法可以简单地记为：1. 线性运算，2. 硬阈值算子。解限制系数的传统算法则是将硬阈值直接换成取绝对值最大的M个系数。

正则项——传统算法：
$$
\mathbf{a}=\arg \min _{\mathbf{a}} \frac{1}{2}\|\mathbf{x}-\mathbf{D} \mathbf{a}\|_{F}^{2}+\lambda\|\mathbf{a}\|_{0}\\
\begin{array}{l}
{\mathbf{a}^{k+1}=h_{\theta}\left(\mathbf{W} \mathbf{x}+\mathbf{S a}^{k}\right)} \\ {\mathbf{W}=\mathbf{D}^{T}, \mathbf{S}=\mathbf{I}-\mathbf{D}^{T} \mathbf{D}, \theta=\lambda^{0.5}}\end{array}
$$
系数个数限制——传统算法：
$$
\mathbf{a}=\arg \min _{\mathbf{a}}\|\mathbf{x}-\mathbf{D} \mathbf{a}\|_{F}^{2} \quad \text { s.t. }\|\mathbf{a}\|_{0} \leq M\\
\mathbf{a}^{k+1}=h_{M}\left(\mathbf{a}^{k}+\mathbf{D}^{\mathrm{T}}\left(\mathbf{x}-\mathbf{D} \mathbf{a}^{k}\right)\right)
$$
作者提出的unfold的网络结构，所有参数都是共享的。都可以学习。对于正则项的结构是将硬阈值算子替换成HELU（也就是硬阈值，只不过有一些细微的改动，并且为了让它具有连续性，用一个近似函数替代了。）

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-16%E4%B8%8B%E5%8D%888.30.50.png)

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-16%E4%B8%8B%E5%8D%888.30.55.png)
$$
\left[\operatorname{HELU}_{\sigma}(\mathbf{u})\right]_{i}=\left\{\begin{array}{cl}{0} & {\text { if }\left|\mathbf{u}_{i}\right| \leq 1-\sigma} \\ {\frac{\left(\mathbf{u}_{i}-1+\sigma\right)}{\left(\mathbf{u}_{i}-1+\sigma\right)}} & {\text { if } 1-\sigma<\mathbf{u}_{i}<1} \\ {\frac{\left(\mathbf{u}_{i}+1-\sigma\right)}{\mathbf{u}_{i}}} & {\text { if }-1<\mathbf{u}_{i}<\sigma-1}\end{array}\right.
$$
另外作者为了增强训练效果，使用了多任务的方法，即结合分类或聚类任务，不详细说明了。基本的出发点都是训练的网络生成的稀疏系数。所以这篇文章叫 learning deep $l_0$ encoder，是因为目标是要得到最好的稀疏系数表达，极小化0范数的目标函数。

### Bibtex:

```latex
@inproceedings{wang2016learning,
  title={Learning deep ℓ 0 encoders},
  author={Wang, Zhangyang and Ling, Qing and Huang, Thomas S},
  booktitle={Thirtieth AAAI Conference on Artificial Intelligence},
  year={2016}
}
```