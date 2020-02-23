### Paper:

Variational Networks: Connecting Variational Methods and Deep Learning

### Author:

Erich Kobler, Teresa Klatzer, Kerstin Hammernik and Thomas Pock

### Year:

2017

### Notes:

这篇文章是将变分模型和残差网络进行比较，提出了变分网络。整体的思路就是借鉴深度网络的学习方式，将变分模型的计算过程变成了网络的形式（并不是卷积）。将其中需要计算的函数用径向基函数来表示，网络学习的是这些参数。因为相关性不强，所以细节看的比较少，附录的内容也没有看，不过总体的想法应该是get了。

设优化目标：
$$
\min _{\boldsymbol{x}} F(\boldsymbol{x}):=\sum_{c=1}^{C} f_{c}\left(\boldsymbol{x} ; \boldsymbol{\theta}_{c}\right)+h(\boldsymbol{x})
$$
那么有如下的 incremental proximal gradient step：
$$
\boldsymbol{x}_{t+1}=\operatorname{prox}_{h}^{\eta_{t}}\left(\boldsymbol{x}_{t}-\eta_{t} \nabla f_{c(t)}\left(\boldsymbol{x}_{t} ; \boldsymbol{\theta}_{c(t)}\right)\right)
$$
<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-26%E4%B8%8B%E5%8D%882.17.00.png" style="zoom:67%;" />

残差单元和变分单元的相似性：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-26%E4%B8%8B%E5%8D%882.37.27.png" style="zoom:50%;" />

最后就是如何参数化作者提出的变分网络。假设目标是：
$$
\min _{\boldsymbol{x} \in \mathcal{X}^{n}} F(\boldsymbol{x}):=\sum_{c=1}^{C} f_{c}\left(\boldsymbol{x} ; \boldsymbol{\theta}_{c}\right)=R_{c}\left(\boldsymbol{x} ; \boldsymbol{\theta}_{c}\right)+D_{c}\left(\boldsymbol{x} ; \boldsymbol{\theta}_{c}\right)
$$
$R_c$ 是先验信息，$D_c$ 是data fidelity，那么有：
$$
R_{c}\left(\boldsymbol{x} ; \boldsymbol{\theta}_{c}\right)=\sum_{i=1}^{N_{r}} \sum_{j=1}^{n} \phi_{i}^{c}\left(\left(K_{i}^{c} \boldsymbol{x}\right)_{j}\right)\\
D_{c}\left(\boldsymbol{x} ; \boldsymbol{\theta}_{c}\right)=\sum_{i=1}^{N_{d}} \sum_{j=1}^{n} \psi_{i}^{c}\left(\left(\bar{K}_{i}^{c}\left(A \boldsymbol{x}-\boldsymbol{x}_{0}\right)\right)_{j}\right)\\
\phi_{i}^{\prime c}(y)=\sum_{j=1}^{N_{w}} \exp \left(-\frac{\left(y-\mu_{j}\right)^{2}}{2 \sigma^{2}}\right) w_{i j}^{c}
$$
感觉作者的做法就是将变分模型展开为网络的形式了，整个网络就是变分模型的求解过程，目标函数就定为解和groundtruth之间的误差：
$$
\min _{\boldsymbol{\theta} \in \mathcal{T}} L(\boldsymbol{\theta}):=\frac{1}{N_{s}} \sum_{s=1}^{N_{s}}\left\|\boldsymbol{x}_{T}^{s}(\boldsymbol{\theta})-\boldsymbol{x}_{g t}^{s}\right\|_{1}
$$

> 思考：这篇文章相当于反过来，借鉴深度学习的框架来实现变分模型的求解。作者在文章的最开始提到传统变分模型很难将重建的信号的结构表示出来，而深度学习似乎具有这种潜力。但从实际效果上看，深度学习也有其不足之处，如何准确地判断其优缺点并结合起来是解决问题的关键。因此就要思考深度学习有别于传统方法的地方。



### Bibtex:

```latex
@inproceedings{kobler2017variational,
  title={Variational networks: connecting variational methods and deep learning},
  author={Kobler, Erich and Klatzer, Teresa and Hammernik, Kerstin and Pock, Thomas},
  booktitle={German conference on pattern recognition},
  pages={281--293},
  year={2017},
  organization={Springer}
}
```