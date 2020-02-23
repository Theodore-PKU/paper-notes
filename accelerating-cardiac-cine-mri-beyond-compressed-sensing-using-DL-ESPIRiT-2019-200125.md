### Paper:

Accelerating cardiac cine MRI beyond compressed sensing using DL-ESPIRiT

### Author:

Christopher M. Sandino, Peng Lai, Shreyas S. Vasanawala, and Joseph Y. Cheng

### Year:

2019

### Notes:

阅读时间：2020.01.25

泛读。这篇文章研究的是MRI的重建，主要的做法结合 parellel imaging 和 deep learning 在 MRI 重建的方法。parellel 部分，作者使用了 ESPIRiT，个人感觉，deep learning 部分和敏感矩阵的估计无关，敏感矩阵的估计仍然是用 ESPIRiT 算法完成的（也可能是自己的理解有问题，ESPIRiT 算法，看得不是特别懂）。deep learning 部分用的是 proximal gradient 算法的网络展开，不过因为是 parellel imaging，所以测量矩阵和一般的MRI 不一样，并且有多个 maps。因此网络中需要用到 3D 卷积或 2D + 1D 卷积。网络结构上，仍是 CNN 部分表示 proximal 算子，加上 data fidelity。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-25%E4%B8%8B%E5%8D%885.06.35.png"/>

其他的并没有特别的地方。

### Bibtex:

```latex
@article{sandino2019accelerating,
  title={Accelerating cardiac cine MRI beyond compressed sensing using DL-ESPIRiT},
  author={Sandino, Christopher M and Lai, Peng and Vasanawala, Shreyas S and Cheng, Joseph Y},
  journal={arXiv preprint arXiv:1911.05845},
  year={2019}
}
```

