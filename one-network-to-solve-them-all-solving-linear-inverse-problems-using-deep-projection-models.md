### Paper:

One Network to Solve Them All — Solving Linear Inverse Problems using Deep Projection Models

### Author:

J. H. Rick Chang, Chun-Liang Li, Barnabas Poczos, B. V. K. Vijaya Kumar, and Aswin C. Sankaranarayanan

### Year:

2017

### Notes:

这篇文章的想法是通过训练一个投影网络实现 proximal 算子，而这个算子可以通用于所有的逆问题中。作者的观察是，使用ADMM算法求解逆问题的时候，总是有一步是对应 prior 的投影计算。作者认为一般的深度学习只能局限于具体任务和具体数据，而传统方法则基本都能用，因此作者想将两者结合起来。方式是用深度学习来表示 proximal 算子，将输入映射到真实图像的流形上，用这个网络来替代 ADMM 算法中的投影步，其他保持不变，这样既可以利用数据来训练，也可以有有很强的泛化性。

作者希望用一个很大的数据集，训练的 proximal 算子对应的 prior 就是图像空间的特征函数。在理论上，作者提出的投影矩阵需要满足一定的条件（最优解），这样替代原来的 proximal 算子后，才能保证 ADMM 收敛。但实际上这一点无法做到，也无法分析在不是最优解的情况下的收敛性。从效果上看，对于较简单的逆问题，表现不错，对于很难的逆问题表现较差（big hole inpainting）。（但我觉得这样做计算量还是很大）。而之所以要替换传统 ADMM 算法中的proximal 算子，原因就是传统的prior 不能完全表示图像空间，比如可以用系数的小波或梯度生成类似噪声的图像。

传统的 ADMM 算法：
$$
\begin{aligned}
&\mathbf{x}^{(k+1)} \leftarrow \underset{\mathbf{x}}{\arg \min } \frac{\rho}{2}\left\|\mathbf{x}-\mathbf{z}^{(k)}+\mathbf{u}^{(k)}\right\|_{2}^{2}+\lambda \phi(\mathbf{x})\\
&\mathbf{z}^{(k+1)} \leftarrow \underset{\mathbf{z}}{\arg \min } \frac{1}{2}\|\mathbf{y}-A \mathbf{z}\|_{2}^{2}+\frac{\rho}{2}\left\|\mathbf{x}^{(k+1)}-\mathbf{z}+\mathbf{u}^{(k)}\right\|_{2}^{2}\\
&\mathbf{u}^{(k+1)} \leftarrow \mathbf{u}^{(k)}+\mathbf{x}^{(k+1)}-\mathbf{z}^{(k+1)}
\end{aligned}
$$
作者就是要用投影网络解第一个子问题，其中的 $\phi(x)$ 就是真实图像流形上的特征函数。作者想实现的投影网络的效果：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-27%E4%B8%8B%E5%8D%884.31.19.png" style="zoom:50%;" />

如何训练投影网络呢？作者的想法是对抗学习，类似于GAN。首先投影网络用一个context 自编码器来表示。用一个分类器 $D$ 的分类来充当特征函数 $\phi(x)$，作者还提出了一个技巧是 $D_l$ ，这个判别器的输入是投影网络的编码。 判别器的目的是将P生成的图像和真实图像区分开，就和GAN 里的判别器一样。作者没有说怎么训练，但是应该和GAN是类似的。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-27%E4%B8%8B%E5%8D%884.35.11.png" style="zoom:50%;" />

投影网络 $P$ 的输入是 perturbation image。这些图像的获取方式是先加上高斯噪声（不同区域的噪声标准差不同），然后平滑图像。投影网络的目标函数为：
$$
\begin{aligned}
&\min _{\boldsymbol{\theta}_{\mathcal{P}}} \sum_{\mathbf{x} \in \mathcal{M}, \mathbf{v} \sim f(\mathbf{x})} \lambda_{1}\|\mathbf{x}-\mathcal{P}(\mathbf{x})\|^{2}+\lambda_{2}\|\mathbf{x}-\mathcal{P}(\mathbf{v})\|^{2}+\cdots\\
&\cdots \lambda_{3}\|\mathbf{v}-\mathcal{P}(\mathbf{v})\|^{2}-\lambda_{4} \log \left(\sigma\left(\mathcal{D}_{\ell} \circ \mathcal{E}(\mathbf{v})\right)\right)-\lambda_{5} \log (\sigma(\mathcal{D} \circ \mathcal{P}(\mathbf{v})))
\end{aligned}
$$
实验：测试的任务有压缩感知、像素级别的去噪和inpainting、小区域inpainting、大区域inpainting、超分辨。比较的对象是基于小波系数稀疏性的算法。作者表示在较为简单的任务上提出的投影网络表现得比较好，但是在更复杂的任务上需要提前终止 。从效果上看，确实这种方法有很强的泛化性（而特定网络则不行，即使是同一个任务，换数据集就不行了），在不同任务上都有比较好的表现（虽然不及特定网络）。但也有失败的任务。在 测试 ImageNet 上的大区域inpainting时失效了。

> 这似乎说明当数据集比较简单的时候，这种方法可以比较有效的记住相关的细节，或者说对应的好的图像是什么，但是对于非常复杂的数据集比如 imagenet，还是很难表达出什么样的图像是好的，但我感觉这也和任务有关，big hole 的填补本身太困难了。如果仅仅是将图像投影到正确的空间中，但如果投影前的图像本身问题就很大就无法收敛了。这个值得思考。

### Bibtex:

```latex
@inproceedings{rick2017one,
  title={One Network to Solve Them All--Solving Linear Inverse Problems Using Deep Projection Models},
  author={Rick Chang, JH and Li, Chun-Liang and Poczos, Barnabas and Vijaya Kumar, BVK and Sankaranarayanan, Aswin C},
  booktitle={Proceedings of the IEEE International Conference on Computer Vision},
  pages={5888--5897},
  year={2017}
}
```