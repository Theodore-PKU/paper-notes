### Paper:

MoDL: Model Based Deep Learning Architecture for Inverse Problems

### Author:

Hemant K. Aggarwal, Merry P. Mani, and Mathews Jacob

### Year:

2018

### Notes:

精读。这篇文章中，作者提出的方法是用一个神经网络作为重建算法中的正则项，将求解过程展开成网络形式。正则项部分的网络是共享参数，通过这种方式减少参数量。这篇文章的主要贡献在于针对较为复杂的问题，在子问题中使用了 CG 的迭代算法而不是常见的 steepest decent。另外作者强调了共享参数这种方法的优势。

在这篇文章中，作者比较了其他利用神经网络作为正则项的方法。那些方法有的是将神经网络的训练作为独立的部分，有的没有使用共享参数。作者认为这些做法都有其缺点（主要是不能很好地学习伪影的统计特征和计算资源需求量大）。作者认为参数少的好处不仅在于计算资源需求小，也有利于在训练数据较少的情况下达到一个比较好的效果。而端到端的训练效果更好，也不需要手动调节去噪网络在训练时的噪声水平。使用 CG 算法的原因是复杂问题情况下（作者在论文中用的是多线圈的MRI）一般的方法很难计算，所以作者考虑用一个 CG 迭代来替换。

这一篇之所以叫 model based 的原因在于作者的做法还是遵循了传统方法的框架，只是将正则项变成一个可以学习的正则项。

模型描述：
$$
\mathbf{x}_{\mathrm{rec}}=\arg \min _{\mathbf{x}} \underbrace{\|\mathcal{A}(\mathbf{x})-\mathbf{b}\|_{2}^{2}}_{\text {data consistency }}+\lambda \underbrace{\left\|\mathcal{N}_{\mathbf{w}}(\mathbf{x})\right\|^{2}}_{\text {regularization }}
$$
其中 $\mathcal{N}_{\mathbf{w}}(\mathbf{x})=\left(\mathcal{I}-\mathcal{D}_{\mathbf{w}}\right)(\mathbf{x})=\mathbf{x}-\mathcal{D}_{\mathbf{w}}(\mathbf{x})$，$\mathcal{D}_{\mathbf{w}}(\mathbf{x})$ 表示网络。迭代算法展开后即为：
$$
\begin{aligned}
\mathbf{x}_{n+1} &=\arg \min _{\mathbf{x}}\|\mathcal{A}(\mathbf{x})-\mathbf{b}\|_{2}^{2}+\lambda\left\|\mathbf{x}-\mathbf{z}_{n}\right\|^{2} \\
\mathbf{z}_{n} &=\mathcal{D}_{\mathbf{w}}\left(\mathbf{x}_{n}\right)
\end{aligned}
$$
常见的做法中，第一步的计算是：
$$
\mathbf{x}_{n+1}=\underbrace{\left(\mathcal{A}^{H} \mathcal{A}+\lambda \mathcal{I}\right)^{-1}}_{\mathcal{Q}}\left(\mathcal{A}^{H}(\mathbf{b})+\lambda \mathbf{z}_{n}\right)
$$
但是在复杂情况下 $\mathcal{Q}$ 无法得到，所以引入 CG 迭代算法（但这部分没有看懂具体要怎么处理）。即使引入了CG 算法，在训练时，作者说通过一个 CG 算法就可以得到反传梯度，因此仍然可以训练网络。训练的损失函数是图像域的2范数。具体的训练方法，作者使用了两阶段训练。因为展开算法后应该会得到 K 个 recursive 的结构，在第一阶段的训练中，作者只使用 1 次迭代。训练得到的参数作为第二阶段训练的初始参数，此时改为 K 次迭代，同时训练。网络的结构比较简单，由卷积、BN、ReLU 构成。

整体框架：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-02%E4%B8%8A%E5%8D%8811.29.10.png" style="zoom:50%;" />

block 结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-02%E4%B8%8A%E5%8D%8811.29.05.png" style="zoom:50%;" />

CNN-based denoiser 结构（整体上有一个残差结构）：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-02%E4%B8%8A%E5%8D%8811.28.54.png" style="zoom:50%;" />

实验部分。作者表示由于其他很多方法并不适用作者提出的这种复杂情形，所以作者仅仅对和作者的方法有相似框架的方法进行了对比（是否共享参数？Denoiser是否端到端训练？使用CG算法还是其他算法？）。

- 作者用一个猫狗的数据 （因为数据量多）当作MRI图像来训练，验证训练数据量对效果的影响。其结果是相较于其他方法即使不用很多数据也能快速达到一个比较好的效果。
- 作者还发现提出的方法如果用某个加速程度的数据来训练，同样可以很好地泛化到其他加速程度的数据中。
- 不同方法之间的差异 
- 架构的迭代次数的影响（作者认为如果训练数据多，考虑的应该是增加每一个 denoiser 的网络大小，继续采用参数共享，而不是每次迭代都用不同的参数）

### Bibtex:

```latex
@article{aggarwal2018modl,
  title={Modl: Model-based deep learning architecture for inverse problems},
  author={Aggarwal, Hemant K and Mani, Merry P and Jacob, Mathews},
  journal={IEEE transactions on medical imaging},
  volume={38},
  number={2},
  pages={394--405},
  year={2018},
  publisher={IEEE}
}
```