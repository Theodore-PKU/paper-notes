### Paper:

Model Based Image Reconstruction Using Deep Learned Priors (MoDL)

### Author:

Hemant Kumar Aggarwal, Merry P. Mani, and Mathews Jacob

### Year:

2019

### Notes:

泛读。这篇文章的作者后来写过一篇 MoDL 的文章，只不过应用范围扩大到 inverse problem，这一篇则是仅仅关注了多线圈 MRI 的重建。方法就没有什么特别的了，CNN 当作去噪步，和 DC 步构成 unroll 的网络。不过因为是多线圈 MRI，这个地方的 DC 和我以前见到的不太一样。作者的做法是每个线圈对应的图像都生成，然后用一个投影操作将每个线圈对应的图像投影到满足 sensitive map 的约束中。其他就没有什么值得注意的地方了。

一般问题情况下的目标函数：
$$
\mathbf{x}=\arg \min _{\mathbf{x}}\|\mathbf{A} \mathbf{x}-\mathbf{b}\|_{2}^{2}+\lambda\left\|\mathbf{x}-\mathcal{D}_{\mathbf{w}}(\mathbf{x})\right\|^{2}
$$
对应的迭代步骤：
$$
\begin{aligned}
\mathbf{z}[n] &=\mathcal{D}_{\mathbf{w}}(\mathbf{x}[n]) \\
\mathbf{x}[n+1] &=\left(\mathbf{A}^{H} \mathbf{A}+\lambda \mathbf{I}\right)^{-1}\left(\mathbf{A}^{H} \mathbf{b}+\lambda \mathbf{z}[n]\right)
\end{aligned}
$$
多线圈的约束：
$$
\mathcal{C}: \mathbf{x}_{i}=\mathbf{c}_{i} \cdot \mathbf{x} ; \forall i=1, \ldots, M
$$
作者设计的投影计算：
$$
\mathcal{P}_{\mathcal{C}}\left(\mathbf{x}_{i}\right)=\frac{\mathbf{c}_{i}^{*} \cdot \mathbf{x}_{i}}{\sum_{i=1}^{M}\left|\mathbf{c}_{i}\right|^{2}} ; \forall i=1, \ldots, M
$$
目标函数就变成：
$$
\mathcal{L}=\sum_{i=1}^{M}\left(\left\|\mathbf{A} \mathbf{x}_{i}-\mathbf{b}_{i}\right\|_{2}^{2}+\lambda_{1}\left\|\mathcal{N}_{\mathbf{w}}\left(\mathbf{x}_{i}\right)\right\|^{2}+\lambda_{2}\left\|\mathbf{x}_{i}-\mathcal{P}_{\mathcal{C}}\left(\mathbf{x}_{i}\right)\right\|^{2}\right)
$$
由此给出的迭代步骤：
$$
\begin{aligned}
\mathbf{z}_{i}[n] &=\mathcal{D}_{\mathbf{w}}\left(\mathbf{x}_{i}[n]\right) \\
\mathbf{y}_{i}[n] &=\mathcal{P}_{\mathcal{C}}\left(\mathbf{z}_{i}[n]\right) \\
\alpha_{i}[n] &=\lambda_{1} \mathbf{z}_{i}[n]+\lambda_{2} \mathbf{y}_{i}[n] \\
\mathbf{x}_{i}[n+1] &=\left(\mathbf{A}^{H} \mathbf{A}+\lambda \mathbf{I}\right)^{-1}\left(\mathbf{A}^{H} \mathbf{b}_{i}+\alpha_{i}[n]\right)
\end{aligned}
$$
可以发现就是多了一个 $\mathcal{P}_c$ 的投影步骤(2)和两者的线性和步骤(3).

### Bibtex:

```latex
@inproceedings{aggarwal2018model,
  title={Model based image reconstruction using deep learned priors (MODL)},
  author={Aggarwal, Hemant Kumar and Mani, Merry P and Jacob, Mathews},
  booktitle={2018 IEEE 15th International Symposium on Biomedical Imaging (ISBI 2018)},
  pages={671--674},
  year={2018},
  organization={IEEE}
}
```

### 其他

