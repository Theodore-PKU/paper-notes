### Paper:

Model-Based Learning for Accelerated, Limited-View 3-D Photoacoustic Tomography

### Author:

Andreas Hauptmann, Felix Lucka, Marta Betcke, Nam Huynh, Jonas Adler Paul Beard, Sebastien Ourselin, and Simon Arridge

### Year:

2018

### Notes:

阅读日期：2020.01.11

泛读。研究的问题是 Photoacoustic Tomography (PAT)。也可以写成线性系统的形式。这篇文章的思路和 variational network 是一样的，只不过没有用 fields of experts，proximal 算子部分用了一个CNN。不过由于是 3D的，所以矩阵运算的开销很大，所以训练的时候是每个迭代block依次训练，而不是一次性训练所有网络 block的参数。

从proximal迭代<img src="http://latex.codecogs.com/svg.latex? x_{k+1}=\operatorname{prox}_{R,\left(\lambda \gamma_{k+1}\right)}\left(x_{k}-\gamma_{k+1} \nabla d\left(y, A x_{k}\right)\right)"  border="0"/> 变成<img src="http://latex.codecogs.com/svg.latex? x_{k+1}=G_{\theta_{k}}\left(\nabla d\left(y, A x_{k}\right), x_{k}\right)"  border="0"/>

CNN的结构为：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-11%E4%B8%8B%E5%8D%884.12.51.png" width="80%"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-11%E4%B8%8B%E5%8D%884.13.02.png"  width="50%"/>

### Bibtex:

```latex
@article{hauptmann2018model,
  title={Model-based learning for accelerated, limited-view 3-d photoacoustic tomography},
  author={Hauptmann, Andreas and Lucka, Felix and Betcke, Marta and Huynh, Nam and Adler, Jonas and Cox, Ben and Beard, Paul and Ourselin, Sebastien and Arridge, Simon},
  journal={IEEE transactions on medical imaging},
  volume={37},
  number={6},
  pages={1382--1393},
  year={2018},
  publisher={IEEE}
}
```

