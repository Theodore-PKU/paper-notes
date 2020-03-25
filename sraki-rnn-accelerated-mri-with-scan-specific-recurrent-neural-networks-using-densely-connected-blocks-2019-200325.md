### Paper:

sRAKI-RNN: Accelerated MRI with Scan-Speciﬁc Recurrent Neural Networks using Densely Connected Blocks

### Author:

Seyed Amir Hossein Hosseini, Chi Zhang, Kâmil Uǧurbil, Steen Moeller, and Mehmet Akçakaya

### Year:

2019

### Notes:

泛读。这篇文章研究的是 MRI 的重建，方法概括起来是把 RAKI 方法和 RNN 结合起来，换句话说就是 unroll 形式的神经网络，只不过 CNN 的投影对象是 kspace，DC 也是直接在 kspace 上进行。因为只考虑 kspace，所以可以用 ACS 数据来训练网络，不需要很大的数据集来训练。CNN 部分主要就是 dense shortcut 的结构。

迭代的形式（ x 是 kspace）
$$
\left\{\begin{array}{l}
\mathbf{z}_{n}=\mathbf{G}\left(\mathbf{x}_{n}\right) \\
\mathbf{x}_{n+1}=\left(\mathbf{I}-\mathbf{D}^{T} \mathbf{D}\right) \mathbf{z}_{n}+\mathbf{D}^{T} \mathbf{y}
\end{array}\right.
$$
<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200325161700.png"/>

### Bibtex:

```latex
@inproceedings{hosseini2019sraki,
  title={sRAKI-RNN: accelerated MRI with scan-specific recurrent neural networks using densely connected blocks},
  author={Hosseini, Seyed Amir Hossein and Zhang, Chi and Uǧurbil, K{\^a}mil and Moeller, Steen and Ak{\c{c}}akaya, Mehmet},
  booktitle={Wavelets and Sparsity XVIII},
  volume={11138},
  pages={111381B},
  year={2019},
  organization={International Society for Optics and Photonics}
}
```

### 其他

