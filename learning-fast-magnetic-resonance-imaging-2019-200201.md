### Paper:

Learning Fast Magnetic Resonance Imaging

### Author:

Tomer Weis, Sanketh Vedula, Ortal Senouf

### Year:

2019

### Notes:

阅读时间：2020.02.01

泛读。这篇文章研究的是 MRI 的重建。作者的想法和之前看到的 [Self-Supervised Deep Active Accelerated MRI](self-supervised-deep-active-accelerated-mri-2019-200131.md) 很像，都是要同时训练 sampling scheme 和重建网络。不过作者认为那一篇文章的方法太过复杂了，而这篇文章中，作者的做法是将笛卡尔采样的 mask 表示成离散和连续的两种形式，利用一个技巧来求导。因此这篇文章不是强化学习，不会根据实际的每个成像进行不同的采样，而是训练后固定采样方式。整体框架是输入为 full kspace，通过 mask layer 变成 undersampled kspace，这个 mask layer 在前传和反传时都当成离散变量的值，只取0-1，计算梯度后，对连续变量进行更新，更新完再根据阈值离散化为 0-1 变量。也就是说连续变量始终是存在的，相当于一个隐藏的变量，可以用来推导出 0-1变量，后者只是用来计算，是表象变量。得到 undersampled kspace 后，进行 zero-fill 重建，再输入到一个 U-Net 结构的重建网络中进行重建。训练时可以端到端地同时训练sampling layer 和重建网络。

sub-sampling 层的初始化是先给一个比较合适的 mask，相对应的，连续变量的初始化根据给定的这个 0-1 mask的初始化，用均匀分布 ($U(0,0.5), U(0.5,1)$) 来随机初始化。作者的实验表明，sampling scheme 在训练后会和初始有所不同，但大体还是一样的，提升效果不多，比起固定的 sampling scheme，就好一点点。感觉这种做法的参考意义不大。

最大的疑惑在于 mask 一开始为 0 的地方，导数不是 0 吗？会变动成为非 0 值？比较怀疑。作者应该试一下初始的sampling mask 比较差的情况。

整体流程：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-01%E4%B8%8A%E5%8D%889.15.22.png" width="80%"/>

算法：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-02-01%E4%B8%8A%E5%8D%889.15.05.png" width="80%"/>

### Bibtex:

```latex
@article{weiss2019learning,
  title={Learning fast magnetic resonance imaging},
  author={Weiss, Tomer and Vedula, Sanketh and Senouf, Ortal and Bronstein, Alex and Michailovich, Oleg and Zibulevsky, Michael},
  journal={arXiv preprint arXiv:1905.09324},
  year={2019}
}
```

