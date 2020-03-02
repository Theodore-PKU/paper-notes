### Paper:

Joint Multi-anatomy Training of a Variational Network for Reconstruction of Accelerated Magnetic Resonance Image Acquisitions

### Author:

Patricia M. Johnson, Matthew J. Muckley, Mary Bruno, Erich Kobler, Kerstin Hammernik, Thomas Pock, and Florian Knoll

### Year:

2019

### Notes:

阅读日期：2020.03.02

泛读。这篇文章很简单，研究的是 MRI 重建，主要内容是测试作者提出的 U-net 版本的 VN 方法是否比原始的 VN 方法更好，另一个内容是测试 joint train 不同的区域的效果和单独用某个区域的数据训练的效果的差别。实验结果是 U-net VN 更好。但是作者没有说 U-net VN 的具体结构，只是说将 regularizer 替换为 U-net 之后，其他都保持不变。U-net 有三层 encoding 和三层 decoding。另外 sensitivities 是用 ESPIRiT 估计的。参考价值不大的文章。

### Bibtex:

```latex
@article{haber2019imexnet,
  title={IMEXnet: A Forward Stable Deep Neural Network},
  author={Haber, Eldad and Lensink, Keegan and Triester, Eran and Ruthotto, Lars},
  journal={arXiv preprint arXiv:1903.02639},
  year={2019}
}
```

### Related work

None

### Code or link

None 