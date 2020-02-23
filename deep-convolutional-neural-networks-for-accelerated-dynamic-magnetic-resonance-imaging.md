### Paper:

Deep convolutional neural networks for accelerated dynamic magnetic resonance imaging

### Author:

Christopher M. Sandino, Neerav Dixit, Joseph Y. Cheng, Shreyas S. Vasanawala

### Year:

2017

### Notes:

这篇文章的主要想法是用一个Unet实现动态心脏MRI的重建。模型本身没有什么新意。输出是残差，和其他的论文也没有差别。考虑了三种不同loss，图像域的2范数、傅立叶域的1范数和ssim loss。这篇文章因为是动态的，所以本应该有时间维度，但是作者忽略了，全部当成slice。另外，作者的泛化实验（训练数据和检验数据不同）表明，泛化性不是那么好。

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-25%E4%B8%8B%E5%8D%883.55.42.png)

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-25%E4%B8%8B%E5%8D%883.55.48.png" style="zoom:50%;" />

### Bibtex:

```latex
@article{sandino2017deep,
  title={Deep convolutional neural networks for accelerated dynamic magnetic resonance imaging},
  author={Sandino, Christopher M and Dixit, Neerav and Cheng, Joseph Y and Vasanawala, Shreyas S},
  journal={preprint},
  year={2017}
}
```