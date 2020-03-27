### Paper:

Artificial Neural Network Enhanced Bayesian PET Image Reconstruction

### Author:

Bao Yang, Leslie Ying, Jing Tang

### Year:

2018

### Notes:

泛读。这篇文章研究的是 PET 重建，方法其实非常简单，属于神经网络投影的类型。具体的做法是用 MLP 训练出 MAP 方法重建的结果到 ground truth 的映射。网络的输入是 patch，而且用了 MAP 方法多种不同权重参数的重建结果，这些不同的参数下的重建结果共同作为网络的输入。网络结构很简单（毕竟 MLP），损失函数是 MSE。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200327164933.png" width="60%"/>

### Bibtex:

```latex
@article{yang2018artificial,
  title={Artificial neural network enhanced Bayesian PET image reconstruction},
  author={Yang, Bao and Ying, Leslie and Tang, Jing},
  journal={IEEE transactions on medical imaging},
  volume={37},
  number={6},
  pages={1297--1309},
  year={2018},
  publisher={IEEE}
}
```

### 其他

