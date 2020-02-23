### Paper:

Training deep learning based image denoisers from undersampled measurements without ground truth and without image prior

### Author:

Magauiya Zhussip, Shakarim Soltanayev, Se Young Chun

### Year:

2018

### Notes:

泛读。这篇文章的想法是只利用测量值，而不利用真实信号来实现压缩感知的重建，因此考虑的重点是只利用 带噪声的图像来训练的框架。重建的整体框架是 D-AMP 算法，不利用真实信号指的是在训练 denoiser 的时候使用 SURE 方法（这种方法的损失函数中没有真实值），利用噪声图片来训练。

一般的 D-AMP 算法框架如图，需要一个训练好的 denoiser

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-04%E4%B8%8B%E5%8D%88.png" style="zoom: 50%;" />

在有真实信号的情况下，去噪模型的训练一般是：
$$
\frac{1}{K} \sum_{j=1}^{K}\left\|\boldsymbol{D}_{\boldsymbol{w}(\sigma)}\left(\boldsymbol{z}^{(j)}\right)-\boldsymbol{x}^{(j)}\right\|^{2}
$$
使用 SURE 方法的损失函数则是（注意这里没有真实信号，比较关键的是 $\sigma$ ）：
$$
\begin{aligned}
\frac{1}{K} \sum_{j=1}^{K}\left\|\boldsymbol{z}^{(j)}-\boldsymbol{D}_{\boldsymbol{w}(\sigma)}\left(\boldsymbol{z}^{(j)}\right)\right\|^{2}-N \sigma^{2}+\\
\frac{2 \sigma^{2} \tilde{\boldsymbol{n}}^{\prime}}{\epsilon}\left(\boldsymbol{D}_{\boldsymbol{w}(\sigma)}\left(\boldsymbol{z}^{(j)}+\epsilon \tilde{\boldsymbol{n}}\right)-\boldsymbol{D}_{\boldsymbol{w}(\sigma)}\left(\boldsymbol{z}^{(j)}\right)\right)
\end{aligned}
$$
作者的整体想法是，将用 SURE 方法作为训练 denoiser 的方式，D-AMP 框架展开。训练步骤是：

1. 先用 BM3D-AMP 算法得到重建的信号，这些信号离真实信号很接近，把它们当作真值初步训练 denoiser，patch 训练，损失函数为 MSE。
2. 有了初始的 denoiser 之后，按照算法 2 的步骤训练。显示将一批测量值传入迭代算法，得到不同程度噪声水平的信号 $s$，用 $s$ 来训练 denoiser，损失函数是 SURE

注意，这里训练时如果一开始估计的噪声比较大，仍然用 BM3D 来去噪，只有噪声水平比较小的时候，才换成自己训练的 denoiser 来训练。关于 denoiser 网络的选择，不是作者讨论的重点。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-04%E4%B8%8B%E5%8D%8812.22.57.png" style="zoom: 50%;" />

作者在论文中还探讨了噪声水平估计对于最终效果的影响。作者认为，在一般的 D-AMP 算法中，对于噪声水平并不敏感，因为去噪模型有真值训练，但是在使用 SURE 的方法时，噪声水平必须估计得比较准确。所以对于不同的测量矩阵，对噪声水平的估计需要一定的调整。

### Bibtex:

```latex
@inproceedings{zhussip2019training,
  title={Training deep learning based image denoisers from undersampled measurements without ground truth and without image prior},
  author={Zhussip, Magauiya and Soltanayev, Shakarim and Chun, Se Young},
  booktitle={Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition},
  pages={10255--10264},
  year={2019}
}
```