# Semi-Supervised Learning with Normalizing Flows

[TOC]

## 信息

Authors: Pavel Izmailov, Polina Kirichenko, Marc Finzi, Andrew Gordon Wilson

Year: ICML 2020

Bibtex:

```
@inproceedings{izmailov2020semi,
  title={Semi-supervised learning with normalizing flows},
  author={Izmailov, Pavel and Kirichenko, Polina and Finzi, Marc and Wilson, Andrew Gordon},
  booktitle={International Conference on Machine Learning},
  pages={4615--4630},
  year={2020},
  organization={PMLR}
}
```

cite: 19

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/semi-supervised-learning-with-normalizing-flows.pdf)



## code

https://github.com/izmailovpavel/flowgmm



## 概括

这篇文章研究的是分类中的半监督问题，采用的方法是 flow-based 生成模型。生成模型用来做分类看起来很奇怪，其实是一件很合理的事情。因为如果知道每个类别的生成模型，就可以计算图像在该类下的概率，最后找到最大概率的类就可以了。这个是生成模型的贝叶斯判别法。和直接的判别模型不同，这种方法虽然理论上没有什么问题，但是实际的效果不如判别模型。

这篇文章的主要创新在于 flow-based 模型的隐变量的设计，作者设计成一个高斯混合分布，每个子类对应一个条件分布。而半监督学习指的是，有些数据有label，有些数据没有label，两类数据对应的损失函数是不同，有label 的数据，计算的是对应类的条件分布的的似然概率，没有 label 的数据则是混合高斯模型的总的概率。

flow 模型采用的是比较原始的 real NVP 模型。这里虽然涉及到隐变量 split 的问题，但是论文里似乎没有详细说这种情况下高斯混合模型是怎么处理的。我跳过这个部分，因为这篇文章整体上来说和 flow-based 模型的创新没有啥关系。另外，作者还是用了 EM 算法来近似训练作者推导得到的损失函数，不过我也不关心。作者的实验比较多，有模拟数据，也有图像数据，也有文本数据（虽然不知道这个在 flow 模型中是如何处理的）。最终的结果是比之前的基于生成模型的分类模型效果好，但仍然比不是直接的判别模型。



## 方法

核心方法是隐变量是高斯混合模型，如下图所示：

![屏幕快照 2021-04-06 上午11.09.34](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-06 上午11.09.34.png)

每个类别是一个条件高斯分布：
$$
\begin{equation}
p_{\mathcal{Z}}(z \mid y=k)=\mathcal{N}\left(z \mid \mu_{k}, \Sigma_{k}\right)
\end{equation}
$$
整体的概率是：
$$
\begin{equation}
p_{\mathcal{Z}}(z)=\frac{1}{\mathcal{C}} \sum_{k=1}^{\mathcal{C}} \mathcal{N}\left(z \mid \mu_{k}, \Sigma_{k}\right)
\end{equation}
$$
这里的一个要求就是类别数据尽量是 balanced。

总的损失函数如下：
$$
\begin{equation}
p_{\mathcal{X}}\left(\mathcal{D}_{\ell}, \mathcal{D}_{u} \mid \theta\right)=\prod_{\left(x_{i}, y_{i}\right) \in \mathcal{D}_{\ell}} p_{\mathcal{X}}\left(x_{i}, y_{i}\right) \prod_{x_{j} \in \mathcal{D}_{u}} p_{\mathcal{X}}\left(x_{j}\right)
\end{equation}
$$
对于有无标签，计算的概率是不同的。

给定类别的概率计算公式是：
$$
\begin{equation}
p_{\mathcal{X}}(x \mid y=k)=\mathcal{N}\left(f(x) \mid \mu_{k}, \Sigma_{k}\right) \cdot\left|\operatorname{det}\left(\frac{\partial f}{\partial x}\right)\right|
\end{equation}
$$
最后计算每个类别的概率是：
$$
\begin{equation}
\begin{aligned}
p_{\mathcal{X}}(y \mid x) &=\frac{p_{\mathcal{X}}(x \mid y) p(y)}{p(x)} \\
&=\frac{\mathcal{N}\left(f(x) \mid \mu_{y}, \Sigma_{y}\right)}{\sum_{k=1}^{\mathcal{C}} \mathcal{N}\left(f(x) \mid \mu_{k}, \Sigma_{k}\right)}
\end{aligned}
\end{equation}
$$
然后找最大的概率的类就可以了。

在半监督学习中，有一个常用方法是一致性正则化，这个方法的主要思路是输入的不同扰动，其某一输出应该是接近的。作者在这个任务上提出的正则化方法是：
$$
\begin{equation}
\begin{array}{l}
L_{\text {cons }}\left(x^{\prime}, x^{\prime \prime}\right)=-\log p\left(x^{\prime} \mid y^{\prime \prime}\right)= \\
\quad-\log \mathcal{N}\left(f\left(x^{\prime}\right) \mid \mu_{y^{\prime \prime}}, \Sigma_{y^{\prime \prime}}\right)-\log \left|\operatorname{det}\left(\frac{\partial f}{\partial x^{\prime}}\right)\right|
\end{array}
\end{equation}
$$
简单来说就是，$x''$ 的类别 $y''$，$x'$ 在 $y''$ 下也应该是高概率。

GMM 每个类别的参数是事先定好的，均值随机，方差都是1。

总的来说思路很简答，这也是一个从隐变量来考虑问题的例子。