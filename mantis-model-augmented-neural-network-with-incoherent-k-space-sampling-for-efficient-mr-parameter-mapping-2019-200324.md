### Paper:

MANTIS: Model‐Augmented Neural neTwork with Incoherent k‐space Sampling for efficient MR parameter mapping

### Author:

Fang Liu, Li Feng, Richard Kijowski

### Year:

2019

### Notes:

泛读。这篇文章研究的是 MRI 的重建，不过和一般的重建问题的区别在于，作者重建的不是图像，而是所谓的 density 和 $T_2$，具体来说就是 $i_{j}=S_{j}\left(\mathrm{I}_{0}, \mathrm{T}_{2}\right)=\mathrm{I}_{0} \cdot e^{-T E_{j} / \mathrm{T}_{2}}$. 作者使用的方法也很简单，就是一个 U-net，输入是 zero-fill 图像，输出是 $I_0, T_2$，损失函数除了这两个参数2范数之外，还包括 kspace 的损失函数，所以整个训练可以写成：
$$
\tilde{\theta}=\arg \min _{\theta}\left(\lambda_{\text {data}} \mathbf{E}_{i_{i} \sim \text {domain}\left(i_{i}\right)}\left[\sum_{j=1}^{t}\left\|E S_{j}\left(C\left(i_{u} | \theta\right)\right)-d_{j}\right\|_{2}^{2}\right]+\lambda_{c m} \mathbf{E}_{i_{i}, \alpha \text { domain}\left(i_{i}\right)}\left[\left\|C\left(i_{u} | \theta\right)-\left(\mathrm{I}_{0}, \mathrm{T}_{2}\right)\right\|_{2}\right]\right)
$$

### Bibtex:

```latex
@article{liu2019mantis,
  title={MANTIS: Model-Augmented Neural neTwork with Incoherent k-space Sampling for efficient MR parameter mapping},
  author={Liu, Fang and Feng, Li and Kijowski, Richard},
  journal={Magnetic resonance in medicine},
  volume={82},
  number={1},
  pages={174--188},
  year={2019},
  publisher={Wiley Online Library}
}
```

### 其他

